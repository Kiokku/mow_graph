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
	- 从`React`v16开始，`componentWillXXX`钩子前增加了`UNSAFE_`前缀。
	-