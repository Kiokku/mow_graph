- > https://react.iamkasong.com/process/completeWork.html
-
- ## 流程概览
	- 类似`beginWork`，`completeWork`也是针对不同`fiber.tag`调用不同的处理逻辑。
	- ```
	  function completeWork(
	    current: Fiber | null,
	    workInProgress: Fiber,
	    renderLanes: Lanes,
	  ): Fiber | null {
	    const newProps = workInProgress.pendingProps;
	  
	    switch (workInProgress.tag) {
	      case IndeterminateComponent:
	      case LazyComponent:
	      case SimpleMemoComponent:
	      case FunctionComponent:
	      case ForwardRef:
	      case Fragment:
	      case Mode:
	      case Profiler:
	      case ContextConsumer:
	      case MemoComponent:
	        return null;
	      case ClassComponent: {
	        // ...省略
	        return null;
	      }
	      case HostRoot: {
	        // ...省略
	        updateHostContainer(workInProgress);
	        return null;
	      }
	      case HostComponent: {
	        // ...省略
	        return null;
	      }
	    // ...省略
	  ```
	- 我们重点关注页面渲染所必须的`HostComponent`（即原生`DOM组件`对应的`Fiber节点`），其他类型`Fiber`的处理留在具体功能实现时讲解。
- ## 处理HostComponent
	- 和`beginWork`一样，我们根据`current === null ?`判断是`mount`还是`update`。
	- 同时针对`HostComponent`，判断`update`时我们还需要考虑`workInProgress.stateNode != null ?`（即该`Fiber节点`是否存在对应的`DOM节点`）
	- ```
	  case HostComponent: {
	    popHostContext(workInProgress);
	    const rootContainerInstance = getRootHostContainer();
	    const type = workInProgress.type;
	  
	    if (current !== null && workInProgress.stateNode != null) {
	      // update的情况
	      // ...省略
	    } else {
	      // mount的情况
	      // ...省略
	    }
	    return null;
	  }
	  ```
- ## update时
	- 当`update`时，`Fiber节点`已经存在对应`DOM节点`，所以不需要生成`DOM节点`。需要做的主要是处理`props`，比如：
		- `onClick`、`onChange`等回调函数的注册
		- 处理`style prop`
		  处理`DANGEROUSLY_SET_INNER_HTML prop`
		  处理`children prop`
	- 我们去掉一些当前不需要关注的功能（比如`ref`）。可以看到最主要的逻辑是调用`updateHostComponent`方法。
	- ```
	  if (current !== null && workInProgress.stateNode != null) {
	    // update的情况
	    updateHostComponent(
	      current,
	      workInProgress,
	      type,
	      newProps,
	      rootContainerInstance,
	    );
	  }
	  ```
	- 你可以从[这里](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactFiberCompleteWork.new.js#L225)看到`updateHostComponent`方法定义。
	- 在`updateHostComponent`内部，被处理完的`props`会被赋值给`workInProgress.updateQueue`，并最终会在`commit阶段`被渲染在页面上。
	- ```
	  workInProgress.updateQueue = (updatePayload: any);
	  ```
	- 其中`updatePayload`为数组形式，他的偶数索引的值为变化的`prop key`，奇数索引的值为变化的`prop value`。
- ## mount时
	- 同样，我们省略了不相关的逻辑。可以看到，`mount`时的主要逻辑包括三个：
		- - 为`Fiber节点`生成对应的`DOM节点`
		  - 将子孙`DOM节点`插入刚生成的`DOM节点`中
		  - 与`update`逻辑中的`updateHostComponent`类似的处理`props`的过程