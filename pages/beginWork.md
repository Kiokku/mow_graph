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
			- [[#green]]==`mount`时==：除[[$blue]]==`fiberRootNode`==以外，`current === null`。会根据`fiber.tag`不同，创建不同类型的`子Fiber节点`
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
	- 我们可以看到，满足如下情况时`didReceiveUpdate === false`（即可以直接复用前一次更新的`子Fiber`，不需要新建`子Fiber`）
		- 1. `oldProps === newProps && workInProgress.type === current.type`，即`props`与`fiber.type`不变。
		- 2. `!includesSomeLane(renderLanes, updateLanes)`，即当前`Fiber节点`优先级不够，会在讲解`Scheduler`时介绍。
	- ```
	  if (current !== null) {
	      const oldProps = current.memoizedProps;
	      const newProps = workInProgress.pendingProps;
	  
	      if (
	        oldProps !== newProps ||
	        hasLegacyContextChanged() ||
	        (__DEV__ ? workInProgress.type !== current.type : false)
	      ) {
	        didReceiveUpdate = true;
	      } else if (!includesSomeLane(renderLanes, updateLanes)) {
	        didReceiveUpdate = false;
	        switch (workInProgress.tag) {
	          // 省略处理
	        }
	        return bailoutOnAlreadyFinishedWork(
	          current,
	          workInProgress,
	          renderLanes,
	        );
	      } else {
	        didReceiveUpdate = false;
	      }
	    } else {
	      didReceiveUpdate = false;
	    }
	  ```
- ## mount时
	- 当不满足优化路径时，我们就进入第二部分，新建`子Fiber`。
	- 我们可以看到，根据`fiber.tag`不同，进入不同类型`Fiber`的创建逻辑。
	- > 可以从[这里](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactWorkTags.js)看到`tag`对应的组件类型
	- 对于我们常见的组件类型，如（`FunctionComponent`/`ClassComponent`/`HostComponent`），最终会进入[reconcileChildren](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L233)方法。
- ## reconcileChildren
	- 从该函数名就能看出这是`Reconciler`模块的核心部分。那么他究竟做了什么呢？
		- [[#green]]==对于`mount`的组件==，他会创建新的`子Fiber节点`
		- [[#green]]==对于`update`的组件==，他会将当前组件与该组件在上次更新时对应的`Fiber节点`比较（也就是俗称的`Diff`算法），将比较的结果生成新`Fiber节点`
	- ```
	  export function reconcileChildren(
	    current: Fiber | null,
	    workInProgress: Fiber,
	    nextChildren: any,
	    renderLanes: Lanes
	  ) {
	    if (current === null) {
	      // 对于mount的组件
	      workInProgress.child = mountChildFibers(
	        workInProgress,
	        null,
	        nextChildren,
	        renderLanes,
	      );
	    } else {
	      // 对于update的组件
	      workInProgress.child = reconcileChildFibers(
	        workInProgress,
	        current.child,
	        nextChildren,
	        renderLanes,
	      );
	    }
	  }
	  ```
	- 从代码可以看出，和`beginWork`一样，他也是通过`current === null ?`区分`mount`与`update`。
	- 不论走哪个逻辑，最终他会生成新的子`Fiber节点`并赋值给`workInProgress.child`，作为本次`beginWork`[返回值](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L1158)，并作为下次`performUnitOfWork`执行时`workInProgress`的[传参](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactFiberWorkLoop.new.js#L1702)。
- ## effectTag
	- 我们知道，`render阶段`的工作是在内存中进行，当工作结束后会通知`Renderer`需要执行的`DOM`操作。要执行`DOM`操作的具体类型就保存在`fiber.effectTag`中。
	- > 你可以从[这里](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactSideEffectTags.js)看到`effectTag`对应的`DOM`操作
	- 比如：
	- ```
	  // DOM需要插入到页面中
	  export const Placement = /*                */ 0b00000000000010;
	  // DOM需要更新
	  export const Update = /*                   */ 0b00000000000100;
	  // DOM需要插入到页面中并更新
	  export const PlacementAndUpdate = /*       */ 0b00000000000110;
	  // DOM需要删除
	  export const Deletion = /*                 */ 0b00000000001000;
	  ```
	- 那么，如果要通知`Renderer`将`Fiber节点`对应的`DOM节点`插入页面中，需要满足两个条件：
		- 1.`fiber.stateNode`存在，即`Fiber节点`中保存了对应的`DOM节点`
		- 2. `(fiber.effectTag & Placement) !== 0`，即`Fiber节点`存在`Placement effectTag`
	- 我们知道，`mount`时，`fiber.stateNode === null`，且在`reconcileChildren`中调用的`mountChildFibers`不会为`Fiber节点`赋值`effectTag`。那么**首屏渲染**如何完成呢？
	- 为了解决这个问题，在`mount`时只有`rootFiber`会赋值`Placement effectTag`，在`commit阶段`只会执行一次插入操作。