- > 在 React 运行时中, 调度中心(位于`scheduler`包), 是整个 React 运行时的中枢(其实是心脏), 所以理解`scheduler`调度, 就基本把握了 React 的命门.
- ## 调度实现
	- `调度中心`最核心的代码, 在[SchedulerHostConfig.default.js](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js)中.
	- ### 内核
	  background-color:: pink
		- 该 js 文件一共导出了 8 个函数, 最核心的逻辑, 就集中在了这 8 个函数中 :
		- ```js
		  export let requestHostCallback; // 请求及时回调: port.postMessage
		  export let cancelHostCallback; // 取消及时回调: scheduledHostCallback = null
		  export let requestHostTimeout; // 请求延时回调: setTimeout
		  export let cancelHostTimeout; // 取消延时回调: cancelTimeout
		  export let shouldYieldToHost; // 是否让出主线程(currentTime >= deadline && needsPaint): 让浏览器能够执行更高优先级的任务(如ui绘制, 用户输入等)
		  export let requestPaint; // 请求绘制: 设置 needsPaint = true
		  export let getCurrentTime; // 获取当前时间
		  export let forceFrameRate; // 强制设置 yieldInterval (让出主线程的周期). 这个函数虽然存在, 但是从源码来看, 几乎没有用到
		  ```
		- 我们知道 react 可以在 nodejs 环境中使用, 所以在不同的 js 执行环境中, 这些函数的实现会有区别. 下面[[#green]]==基于普通浏览器环境, 对这 8 个函数逐一分析 :==
			- **调度相关:** 请求或取消调度
			  logseq.order-list-type:: number
				- [requestHostCallback](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L224-L230)
				- [cancelHostCallback](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L232-L234)
				- [requestHostTimeout](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L236-L240)
				- [cancelHostTimeout](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L242-L245)
				- 这 4 个函数源码很简洁, 非常好理解, 它们的目的就是请求执行(或取消)回调函数. 现在重点介绍其中的`及时回调`(`延时回调`的 2 个函数暂时属于保留 api, 17.0.2 版本其实没有用上)
					- ```js
					  // 接收 MessageChannel 消息
					  const performWorkUntilDeadline = () => {
					    // ...省略无关代码
					    if (scheduledHostCallback !== null) {
					      const currentTime = getCurrentTime();
					      // 更新deadline
					      deadline = currentTime + yieldInterval;
					      // 执行callback
					      scheduledHostCallback(hasTimeRemaining, currentTime);
					    } else {
					      isMessageLoopRunning = false;
					    }
					  };
					  
					  const channel = new MessageChannel();
					  const port = channel.port2;
					  channel.port1.onmessage = performWorkUntilDeadline;
					  
					  // 请求回调
					  requestHostCallback = function (callback) {
					    // 1. 保存callback
					    scheduledHostCallback = callback;
					    if (!isMessageLoopRunning) {
					      isMessageLoopRunning = true;
					      // 2. 通过 MessageChannel 发送消息
					      port.postMessage(null);
					    }
					  };
					  // 取消回调
					  cancelHostCallback = function () {
					    scheduledHostCallback = null;
					  };
					  ```
				- 请求回调之后`scheduledHostCallback = callback`, 然后通过`MessageChannel`发消息的方式触发`performWorkUntilDeadline`函数, 最后执行回调`scheduledHostCallback`.
				- [[#blue]]==此处需要注意: `MessageChannel`在浏览器事件循环中属于`宏任务`, 所以调度中心永远是`异步执行`回调函数==
			- **时间切片(`time slicing`)相关**: 执行时间分割, 让出主线程(把控制权归还浏览器, 浏览器可以处理用户输入, UI 绘制等紧急任务).
			  logseq.order-list-type:: number
				- [getCurrentTime](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L22-L24): 获取当前时间
				- [shouldYieldToHost](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L129-L152): 是否让出主线程
				- [requestPaint](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L154-L156): 请求绘制
				- [forceFrameRate](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/forks/SchedulerHostConfig.default.js#L168-L183): 强制设置 `yieldInterval`(从源码中的引用来看, 算一个保留函数, 其他地方没有用到)
					- ```js
					  const localPerformance = performance;
					  // 获取当前时间
					  getCurrentTime = () => localPerformance.now();
					  
					  // 时间切片周期, 默认是5ms(如果一个task运行超过该周期, 下一个task执行之前, 会把控制权归还浏览器)
					  let yieldInterval = 5;
					  
					  let deadline = 0;
					  const maxYieldInterval = 300;
					  let needsPaint = false;
					  const scheduling = navigator.scheduling;
					  // 是否让出主线程
					  shouldYieldToHost = function () {
					    const currentTime = getCurrentTime();
					    if (currentTime >= deadline) {
					      if (needsPaint || scheduling.isInputPending()) {
					        // There is either a pending paint or a pending input.
					        return true;
					      }
					      // There's no pending input. Only yield if we've reached the max
					      // yield interval.
					      return currentTime >= maxYieldInterval; // 在持续运行的react应用中, currentTime肯定大于300ms, 这个判断只在初始化过程中才有可能返回false
					    } else {
					      // There's still time left in the frame.
					      return false;
					    }
					  };
					  
					  // 请求绘制
					  requestPaint = function () {
					    needsPaint = true;
					  };
					  
					  // 设置时间切片的周期
					  forceFrameRate = function (fps) {
					    if (fps < 0 || fps > 125) {
					      // Using console['error'] to evade Babel and ESLint
					      console['error'](
					        'forceFrameRate takes a positive int between 0 and 125, ' +
					          'forcing frame rates higher than 125 fps is not supported',
					      );
					      return;
					    }
					    if (fps > 0) {
					      yieldInterval = Math.floor(1000 / fps);
					    } else {
					      // reset the framerate
					      yieldInterval = 5;
					    }
					  };
					  ```
				- [[#green]]==注意`shouldYieldToHost`的判定条件:==
					- [[#blue]]==`currentTime >= deadline`==: 只有时间超过`deadline`之后才会让出主线程(其中`deadline = currentTime + yieldInterval`).
						- `yieldInterval`默认是`5ms`, 只能通过`forceFrameRate`函数来修改.
						- 如果一个`task`运行时间超过`5ms`, 下一个`task`执行之前, 会把控制权归还浏览器.
					- [[#green]]==`navigator.scheduling.isInputPending()`==: 这是 facebook 官方贡献给 Chromium 的 api, 现在已经列入 W3C 标准([具体解释](https://engineering.fb.com/2019/04/22/developer-tools/isinputpending-api/)), 用于判断是否有输入事件(包括: input 框输入事件, 点击事件等).