- > https://react.iamkasong.com/process/beginWork.html
-
- ## 方法概览
	- 可以从[源码这里](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L3075)看到`beginWork`的定义。整个方法大概有500行代码。
	- 从上一节我们已经知道，`beginWork`的工作是传入`当前Fiber节点`，创建`子Fiber节点`，我们从传参来看看具体是如何做的。
	- ### 从传参看方法执行
	  background-color:: pink
		- ```
		  function beginWork(
		    current: Fiber | null,
		    workInProgress: Fiber,
		    renderLanes: Lanes,
		  ): Fiber | null {
		    // ...省略函数体
		  }
		  ```
		- 其中传参：
			- [[#green]]==current==：当前组件对应的`Fiber节点`在上一次更新时的`Fiber节点`，即`workInProgress.alternate`
			- [[#green]]==workInProgress==：当前组件对应的`Fiber节点`
			- [[#green]]==renderLanes==：优先级相关，在讲解`Scheduler`时再讲解
		-