- > https://react.iamkasong.com/renderer/prepare.html
-
- `commitRoot`方法是`commit阶段`工作的起点。`fiberRootNode`会作为传参。
- `commit`阶段的主要工作（即`Renderer`的工作流程）分为三部分：
	- [[#blue]]==before mutation 阶段==（执行`DOM`操作前）
	- [[#blue]]==mutation 阶段==（执行`DOM`操作）
	- [[#blue]]==layout 阶段==（执行`DOM`操作后）
- ## before mutation 之前
	- `commitRootImpl`方法中直到第一句`if (firstEffect !== null)`之前属于`before mutation`之前。
	- `before mutation`之前主要做一些变量赋值，状态重置的工作。
- ## layout 之后
	- ```
	  const rootDidHavePassiveEffects = rootDoesHavePassiveEffects;
	  
	  // useEffect相关
	  if (rootDoesHavePassiveEffects) {
	    rootDoesHavePassiveEffects = false;
	    rootWithPendingPassiveEffects = root;
	    pendingPassiveEffectsLanes = lanes;
	    pendingPassiveEffectsRenderPriority = renderPriorityLevel;
	  } else {
	  }
	  
	  // 性能优化相关
	  if (remainingLanes !== NoLanes) {
	    if (enableSchedulerTracing) {
	      // ...
	    }
	  } else {
	    // ...
	  }
	  
	  // 性能优化相关
	  if (enableSchedulerTracing) {
	    if (!rootDidHavePassiveEffects) {
	      // ...
	    }
	  }
	  
	  // ...检测无限循环的同步任务
	  if (remainingLanes === SyncLane) {
	    // ...
	  }
	  
	  // 在离开commitRoot函数前调用，触发一次新的调度，确保任何附加的任务被调度
	  ensureRootIsScheduled(root, now());
	  
	  // ...处理未捕获错误及老版本遗留的边界问题
	  
	  // 执行同步任务，这样同步任务不需要等到下次事件循环再执行
	  // 比如在 componentDidMount 中执行 setState 创建的更新会在这里被同步执行
	  // 或useLayoutEffect
	  flushSyncCallbackQueue();
	  
	  return null;
	  
	  ```
	- 1. `useEffect`相关的处理。
	- 2. 性能追踪相关。
	- 3. 在`commit`阶段会触发一些生命周期钩子（如 `componentDidXXX`）和`hook`（如`useLayoutEffect`、`useEffect`）。