- > https://react.iamkasong.com/renderer/beforeMutation.html
-
- >> `before mutation阶段`的代码很短，整个过程就是遍历`effectList`并调用`commitBeforeMutationEffects`函数处理。
	- ```
	  // 保存之前的优先级，以同步优先级执行，执行完毕后恢复之前优先级
	  const previousLanePriority = getCurrentUpdateLanePriority();
	  setCurrentUpdateLanePriority(SyncLanePriority);
	  
	  // 将当前上下文标记为CommitContext，作为commit阶段的标志
	  const prevExecutionContext = executionContext;
	  executionContext |= CommitContext;
	  
	  // 处理focus状态
	  focusedInstanceHandle = prepareForCommit(root.containerInfo);
	  shouldFireAfterActiveInstanceBlur = false;
	  
	  // beforeMutation阶段的主函数
	  commitBeforeMutationEffects(finishedWork);
	  
	  focusedInstanceHandle = null;
	  ```
- ## commitBeforeMutationEffects
	- ```
	  function commitBeforeMutationEffects() {
	    while (nextEffect !== null) {
	      const current = nextEffect.alternate;
	  
	      if (!shouldFireAfterActiveInstanceBlur && focusedInstanceHandle !== null) {
	        // ...focus blur相关
	      }
	  
	      const effectTag = nextEffect.effectTag;
	  
	      // 调用getSnapshotBeforeUpdate
	      if ((effectTag & Snapshot) !== NoEffect) {
	        commitBeforeMutationEffectOnFiber(current, nextEffect);
	      }
	  
	      // 调度useEffect
	      if ((effectTag & Passive) !== NoEffect) {
	        if (!rootDoesHavePassiveEffects) {
	          rootDoesHavePassiveEffects = true;
	          scheduleCallback(NormalSchedulerPriority, () => {
	            flushPassiveEffects();
	            return null;
	          });
	        }
	      }
	      nextEffect = nextEffect.nextEffect;
	    }
	  }
	  ```
	- 处理`DOM节点`渲染/删除后的 `autoFocus`、`blur` 逻辑。
	  logseq.order-list-type:: number
	- 调用`getSnapshotBeforeUpdate`生命周期钩子。
	  logseq.order-list-type:: number
	- 调度`useEffect`。
	  logseq.order-list-type:: number
- ## 调用getSnapshotBeforeUpdate
	- 从`React`v16开始，`componentWillXXX`钩子前增加了[[#red]]==`UNSAFE_`==前缀。
	- 究其原因，是因为`Stack Reconciler`重构为`Fiber Reconciler`后，`render阶段`的任务可能中断/重新开始，对应的组件在`render阶段`的生命周期钩子（即`componentWillXXX`）可能触发多次。
	- 这种行为和`React`v15不一致，所以标记为`UNSAFE_`。
	- `React`提供了替代的生命周期钩子`getSnapshotBeforeUpdate`。
	- `getSnapshotBeforeUpdate`是在`commit阶段`内的`before mutation阶段`调用的，由于`commit阶段`是同步的，所以不会遇到多次调用的问题。
- ## 调度 `useEffect`
	- ```
	  // 调度useEffect
	  if ((effectTag & Passive) !== NoEffect) {
	    if (!rootDoesHavePassiveEffects) {
	      rootDoesHavePassiveEffects = true;
	      scheduleCallback(NormalSchedulerPriority, () => {
	        // 触发useEffect
	        flushPassiveEffects();
	        return null;
	      });
	    }
	  }
	  ```
	- 异步调度的回调函数就是触发`useEffect`的方法`flushPassiveEffects`。
	- 接下来讨论`useEffect`如何被异步调度，以及为什么要异步（而不是同步）调度。
	- ### 如何异步调度
	  background-color:: green
		- `effectList`中保存了需要执行副作用的`Fiber节点`。其中副作用包括:
			- 插入`DOM节点`（Placement）
			  logseq.order-list-type:: number
			- 更新`DOM节点`（Update）
			  logseq.order-list-type:: number
			- 删除`DOM节点`（Deletion）
			  logseq.order-list-type:: number
		- 整个`useEffect`异步调用分为三步：
			- `before mutation阶段`在`scheduleCallback`中调度`flushPassiveEffects`
			  logseq.order-list-type:: number
			- `layout阶段`之后将`effectList`赋值给`rootWithPendingPassiveEffects`
			  logseq.order-list-type:: number
			- `scheduleCallback`触发`flushPassiveEffects`，`flushPassiveEffects`内部遍历`rootWithPendingPassiveEffects` （即`effectList`）
			  logseq.order-list-type:: number
	- ### 为什么需要异步调用
	  background-color:: green
		- `useEffect`异步执行的原因主要是防止同步执行时阻塞浏览器渲染。