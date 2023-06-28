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
		- 除[`rootFiber`](https://react.iamkasong.com/process/doubleBuffer.html#mount%E6%97%B6)以外， 组件`mount`时，由于是首次渲染，是不存在当前组件对应的`Fiber节点`在上一次更新时的`Fiber节点`，即`mount`时`current === null`。
		- 组件`update`时，由于之前已经`mount`过，所以`current !== null`。
		- 所以我们可以[[#blue]]==通过`current === null ?`来区分组件是处于`mount`还是`update`。==
		- 基于此，`beginWork`的工作可以分为两部分：
			- [[#green]]==`update`时==：如果`current`存在，在满足一定条件时可以复用`current`节点，这样就能克隆`current.child`作为`workInProgress.child`，而不需要新建`workInProgress.child`。
			- [[#green]]==`mount`时==：除`fiberRootNode`以外，`current === null`。会根据`fiber.tag`不同，创建不同类型的`子Fiber节点`
		- ```
		  function beginWork(
		    current: Fiber | null,
		    workInProgress: Fiber,
		    renderLanes: Lanes
		  ): Fiber | null {
		  
		    // update时：如果current存在可能存在优化路径，可以复用current（即上一次更新的Fiber节点）
		    if (current !== null) {
		      // ...省略
		  
		      // 复用current
		      return bailoutOnAlreadyFinishedWork(
		        current,
		        workInProgress,
		        renderLanes,
		      );
		    } else {
		      didReceiveUpdate = false;
		    }
		  
		    // mount时：根据tag不同，创建不同的子Fiber节点
		    switch (workInProgress.tag) {
		      case IndeterminateComponent: 
		        // ...省略
		      case LazyComponent: 
		        // ...省略
		      case FunctionComponent: 
		        // ...省略
		      case ClassComponent: 
		        // ...省略
		      case HostRoot:
		        // ...省略
		      case HostComponent:
		        // ...省略
		      case HostText:
		        // ...省略
		      // ...省略其他类型
		    }
		  }
		  ```
- ## update时