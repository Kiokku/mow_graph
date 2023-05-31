- > https://react.iamkasong.com/preparation/newConstructure.html
-
- ## React16架构
	- React16架构可以分为三层：
		- `Scheduler（调度器）`—— 调度任务的优先级，高优任务优先进入**Reconciler**
		- `Reconciler（协调器）`—— 负责找出变化的组件
		- `Renderer（渲染器）`—— 负责将变化的组件渲染到页面上
	- ### Scheduler（调度器）
	  background-color:: blue
		- 以浏览器是否有剩余时间作为任务中断的标准，当浏览器有剩余时间时通知我们。
		- 部分浏览器已经实现了这个API，这就是[requestIdleCallback (opens new window)](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback)。但是由于以下因素，`React`放弃使用：
			- 1. 浏览器兼容性
			- 2. 触发频率不稳定，受很多因素影响。比如当我们的浏览器切换tab后，之前tab注册的`requestIdleCallback`触发的频率会变得很低
		- 基于以上原因，`React`实现了功能更完备的`requestIdleCallback`polyfill，这就是**Scheduler**。除了在空闲时触发回调的功能外，**Scheduler**还提供了多种调度优先级供任务设置。
		- > [Scheduler (opens new window)](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/scheduler/README.md)是独立于`React`的库
	- ### Reconciler（协调器）
	  background-color:: blue
		- 更新工作从递归变成了可以中断的循环过程。每次循环都会调用`shouldYield`判断当前是否有剩余时间。
		- ```
		  /** @noinline */
		  function workLoopConcurrent() {
		    // Perform work until Scheduler asks us to yield
		    while (workInProgress !== null && !shouldYield()) {
		      workInProgress = performUnitOfWork(workInProgress);
		    }
		  }
		  ```
		-
		-
		-
		-