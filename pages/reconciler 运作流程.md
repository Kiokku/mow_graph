- > 本节从宏观上分析了`reconciler 运作流程`, 并将其分为了 4 个步骤, 基本覆盖了`react-reconciler`包的核心逻辑.
-
- ## 概览
	- `react-reconciler`包的主要作用, 将主要功能分为 4 个方面：
		- **输入**: 暴露`api`函数(如: `scheduleUpdateOnFiber`), 供给其他包(如`react`包)调用.
		  logseq.order-list-type:: number
		- **注册调度任务**: 与调度中心(`scheduler`包)交互, 注册调度任务`task`, 等待任务回调.
		  logseq.order-list-type:: number
		- **执行任务回调**: 在内存中构造出`fiber树`, 同时与与渲染器(`react-dom`)交互, 在内存中创建出与`fiber`对应的`DOM`节点.
		  logseq.order-list-type:: number
		- **输出**: 与渲染器(`react-dom`)交互, 渲染`DOM`节点.
		  logseq.order-list-type:: number
		- ![image.png](../assets/image_1699023235504_0.png)
- ## 分解
	- ### 输入
	  background-color:: pink
		- 在`ReactFiberWorkLoop.js`中, 承接输入的函数只有`scheduleUpdateOnFiber`[源码地址](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L517-L619). 在`react-reconciler`对外暴露的 api 函数中, [[#green]]==只要涉及到需要改变 fiber 的操作(无论是`首次渲染`或`后续更新`操作), 最后都会间接调用`scheduleUpdateOnFiber`==, 所以`scheduleUpdateOnFiber`函数是输入链路中的`必经之路`.
			- ```js
			  // 唯一接收输入信号的函数
			  export function scheduleUpdateOnFiber(
			    fiber: Fiber,
			    lane: Lane,
			    eventTime: number,
			  ) {
			    // ... 省略部分无关代码
			    const root = markUpdateLaneFromFiberToRoot(fiber, lane);
			    if (lane === SyncLane) {
			      if (
			        (executionContext & LegacyUnbatchedContext) !== NoContext &&
			        (executionContext & (RenderContext | CommitContext)) === NoContext
			      ) {
			        // 直接进行`fiber构造`
			        performSyncWorkOnRoot(root);
			      } else {
			        // 注册调度任务, 经过`Scheduler`包的调度, 间接进行`fiber构造`
			        ensureRootIsScheduled(root, eventTime);
			      }
			    } else {
			      // 注册调度任务, 经过`Scheduler`包的调度, 间接进行`fiber构造`
			      ensureRootIsScheduled(root, eventTime);
			    }
			  }
			  ```
		- 逻辑进入到`scheduleUpdateOnFiber`之后, 后面有 2 种可能：
			- 不经过调度, 直接进行`fiber构造`.
			  logseq.order-list-type:: number
			- 注册调度任务, 经过`Scheduler`包的调度, 间接进行`fiber构造`.
			  logseq.order-list-type:: number
	- ### 注册调度任务
	  background-color:: pink
		- 与`输入`环节紧密相连, `scheduleUpdateOnFiber`函数之后, 立即进入`ensureRootIsScheduled`函数([源码地址](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L674-L736)):
			- ```js
			  // ... 省略部分无关代码
			  function ensureRootIsScheduled(root: FiberRoot, currentTime: number) {
			    // 前半部分: 判断是否需要注册新的调度
			    const existingCallbackNode = root.callbackNode;
			    const nextLanes = getNextLanes(
			      root,
			      root === workInProgressRoot ? workInProgressRootRenderLanes : NoLanes,
			    );
			    const newCallbackPriority = returnNextLanesPriority();
			    if (nextLanes === NoLanes) {
			      return;
			    }
			    if (existingCallbackNode !== null) {
			      const existingCallbackPriority = root.callbackPriority;
			      if (existingCallbackPriority === newCallbackPriority) {
			        return;
			      }
			      cancelCallback(existingCallbackNode);
			    }
			  
			    // 后半部分: 注册调度任务
			    let newCallbackNode;
			    if (newCallbackPriority === SyncLanePriority) {
			      newCallbackNode = scheduleSyncCallback(
			        performSyncWorkOnRoot.bind(null, root),
			      );
			    } else if (newCallbackPriority === SyncBatchedLanePriority) {
			      newCallbackNode = scheduleCallback(
			        ImmediateSchedulerPriority,
			        performSyncWorkOnRoot.bind(null, root),
			      );
			    } else {
			      const schedulerPriorityLevel =
			        lanePriorityToSchedulerPriority(newCallbackPriority);
			      newCallbackNode = scheduleCallback(
			        schedulerPriorityLevel,
			        performConcurrentWorkOnRoot.bind(null, root),
			      );
			    }
			    root.callbackPriority = newCallbackPriority;
			    root.callbackNode = newCallbackNode;
			  }
			  ```
		- `ensureRootIsScheduled`的逻辑很清晰, 分为 2 部分:
			- 前半部分: 判断是否需要注册新的调度(如果无需新的调度, 会退出函数).
			  logseq.order-list-type:: number
			- 后半部分: 注册调度任务
			  logseq.order-list-type:: number
				- `performSyncWorkOnRoot`或`performConcurrentWorkOnRoot`被封装到了任务回调(`scheduleCallback`)中;
				  logseq.order-list-type:: number
				- 等待调度中心执行任务, [[#green]]==任务运行其实就是执行`performSyncWorkOnRoot`或`performConcurrentWorkOnRoot`==
				  logseq.order-list-type:: number
	- ### 执行任务回调
	  background-color:: pink
		- [performSyncWorkOnRoot](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L965-L1045):
			- ```js
			  // ... 省略部分无关代码
			  function performSyncWorkOnRoot(root) {
			    let lanes;
			    let exitStatus;
			  
			    lanes = getNextLanes(root, NoLanes);
			    // 1. fiber树构造
			    exitStatus = renderRootSync(root, lanes);
			  
			    // 2. 异常处理: 有可能fiber构造过程中出现异常
			    if (root.tag !== LegacyRoot && exitStatus === RootErrored) {
			      // ...
			    }
			  
			    // 3. 输出: 渲染fiber树
			    const finishedWork: Fiber = (root.current.alternate: any);
			    root.finishedWork = finishedWork;
			    root.finishedLanes = lanes;
			    commitRoot(root);
			  
			    // 退出前再次检测, 是否还有其他更新, 是否需要发起新调度
			    ensureRootIsScheduled(root, now());
			    return null;
			  }
			  ```
			- `performSyncWorkOnRoot`的逻辑很清晰, 分为 3 部分:
				- fiber 树构造.
				  logseq.order-list-type:: number
				- 异常处理: 有可能 fiber 构造过程中出现异常.
				  logseq.order-list-type:: number
				- 调用输出.
				  logseq.order-list-type:: number
		- [performConcurrentWorkOnRoot](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L740-L839):
			- ```js
			  // ... 省略部分无关代码
			  function performConcurrentWorkOnRoot(root) {
			  
			    const originalCallbackNode = root.callbackNode;
			  
			    // 1. 刷新pending状态的effects, 有可能某些effect会取消本次任务
			    const didFlushPassiveEffects = flushPassiveEffects();
			    if (didFlushPassiveEffects) {
			      if (root.callbackNode !== originalCallbackNode) {
			        // 任务被取消, 退出调用
			        return null;
			      } else {
			        // Current task was not canceled. Continue.
			      }
			    }
			    // 2. 获取本次渲染的优先级
			    let lanes = getNextLanes(
			      root,
			      root === workInProgressRoot ? workInProgressRootRenderLanes : NoLanes,
			    );
			    // 3. 构造fiber树
			    let exitStatus = renderRootConcurrent(root, lanes);
			  
			    if (
			      includesSomeLane(
			        workInProgressRootIncludedLanes,
			        workInProgressRootUpdatedLanes,
			      )
			    ) {
			      // 如果在render过程中产生了新的update, 且新update的优先级与最初render的优先级有交集
			      // 那么最初render无效, 丢弃最初render的结果, 等待下一次调度
			      prepareFreshStack(root, NoLanes);
			    } else if (exitStatus !== RootIncomplete) {
			      // 4. 异常处理: 有可能fiber构造过程中出现异常
			      if (exitStatus === RootErrored) {
			        // ...
			      }.
			      const finishedWork: Fiber = (root.current.alternate: any);
			      root.finishedWork = finishedWork;
			      root.finishedLanes = lanes;
			      // 5. 输出: 渲染fiber树
			      finishConcurrentRender(root, exitStatus, lanes);
			    }
			  
			    // 退出前再次检测, 是否还有其他更新, 是否需要发起新调度
			    ensureRootIsScheduled(root, now());
			    if (root.callbackNode === originalCallbackNode) {
			      // 渲染被阻断, 返回一个新的performConcurrentWorkOnRoot函数, 等待下一次调用
			      return performConcurrentWorkOnRoot.bind(null, root);
			    }
			    return null;
			  }
			  ```
			- `performConcurrentWorkOnRoot`的逻辑与`performSyncWorkOnRoot`的不同之处在于, [[#blue]]==对于`可中断渲染`的支持==：
				- 调用`performConcurrentWorkOnRoot`函数时, 首先检查是否处于`render`过程中, 是否需要恢复上一次渲染.
				  logseq.order-list-type:: number
				- 如果本次渲染被中断, 最后返回一个新的 performConcurrentWorkOnRoot 函数, 等待下一次调用.
				  logseq.order-list-type:: number
	- ### 输出
	  background-color:: pink
		- [`commitRoot`](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L1879-L2254):
			- ```js
			  // ... 省略部分无关代码
			  function commitRootImpl(root, renderPriorityLevel) {
			    // 设置局部变量
			    const finishedWork = root.finishedWork;
			    const lanes = root.finishedLanes;
			  
			    // 清空FiberRoot对象上的属性
			    root.finishedWork = null;
			    root.finishedLanes = NoLanes;
			    root.callbackNode = null;
			  
			    // 提交阶段
			    let firstEffect = finishedWork.firstEffect;
			    if (firstEffect !== null) {
			      const prevExecutionContext = executionContext;
			      executionContext |= CommitContext;
			      // 阶段1: dom突变之前
			      nextEffect = firstEffect;
			      do {
			        commitBeforeMutationEffects();
			      } while (nextEffect !== null);
			  
			      // 阶段2: dom突变, 界面发生改变
			      nextEffect = firstEffect;
			      do {
			        commitMutationEffects(root, renderPriorityLevel);
			      } while (nextEffect !== null);
			      root.current = finishedWork;
			  
			      // 阶段3: layout阶段, 调用生命周期componentDidUpdate和回调函数等
			      nextEffect = firstEffect;
			      do {
			        commitLayoutEffects(root, lanes);
			      } while (nextEffect !== null);
			      nextEffect = null;
			      executionContext = prevExecutionContext;
			    }
			    ensureRootIsScheduled(root, now());
			    return null;
			  }
			  ```
		- 在输出阶段,`commitRoot`的实现逻辑是在`commitRootImpl`函数中, 其主要逻辑是处理副作用队列, 将最新的 fiber 树结构反映到 DOM 上.
		- 核心逻辑分为 3 个步骤:
			- [[#green]]==`commitBeforeMutationEffects`==
			  logseq.order-list-type:: number
				- **dom 变更之前**, 主要处理副作用队列中带有`Snapshot`,`Passive`标记的`fiber`节点.
			- [[#green]]==`commitMutationEffects`==
			  logseq.order-list-type:: number
				- **dom 变更**, 界面得到更新. 主要处理副作用队列中带有`Placement`, `Update`, `Deletion`, `Hydrating`标记的`fiber`节点.
			- [[#green]]==`commitLayoutEffects`==
			  logseq.order-list-type:: number
				- **dom 变更后**, 主要处理副作用队列中带有`Update | Callback`标记的`fiber`节点.