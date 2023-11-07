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
			- 调度相关: 请求或取消调度
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
			- logseq.order-list-type:: number