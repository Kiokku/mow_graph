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
	-