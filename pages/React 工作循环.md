- 在概览图中, 可以看到有两个大的循环 (**构建 fiber 树循环**和**任务调度循环**), 它们分别位于`scheduler`和`react-reconciler`包中，本文将这两个循环分别表述为`任务调度循环`和`fiber构造循环`。
- {{embed ((653be810-7c0f-4e94-b503-5869611fa99a))}}
	- `任务调度循环`
	  logseq.order-list-type:: number
		- 源码位于[`Scheduler.js`](https://github.com/facebook/react/blob/v17.0.2/packages/scheduler/src/Scheduler.js), 它是`react`应用得以运行的保证, 它需要循环调用, 控制所有任务(`task`)的调度.
	- `fiber构造循环`
	  logseq.order-list-type:: number
		- 源码位于[`ReactFiberWorkLoop.js`](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js), 控制 fiber 树的构造, 整个过程是一个[深度优先遍历](https://7km.top/algorithm/dfs).
- 这两个循环对应的 js 源码不同于其他闭包(运行时就是闭包), 其中定义的全局变量, 不仅是该作用域的私有变量, 更用于`控制react应用的执行过程`.
- ## 区别与联系
	-