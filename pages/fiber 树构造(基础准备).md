- > 在 React 运行时中, `fiber树构造`位于`react-reconciler`包.
  >
  > 1. 从`scheduler`调度中心的角度来看, 它是任务队列`taskQueue`中的一个具体的任务回调(`task.callback`).
  > 2. 从[React 工作循环](https://7km.top/main/workloop)的角度来看, 它属于`fiber树构造循环`.
- ## ReactElement, Fiber, DOM 三者的关系
	- ### 1. [ReactElement 对象](https://github.com/facebook/react/blob/v17.0.2/packages/react/src/ReactElement.js#L126-L146)(type 定义在[shared 包中](https://github.com/facebook/react/blob/v17.0.2/packages/shared/ReactElementType.js#L15))
	  background-color:: pink
		- 所有采用`jsx`语法书写的节点, 都会被编译器转换, 最终会以`React.createElement(...)`的方式, 创建出来一个与之对应的`ReactElement`对象.
	- ### 2. [fiber 对象](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiber.old.js#L116-L155)(type 类型的定义在[ReactInternalTypes.js](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactInternalTypes.js#L47-L174)中)
	  background-color:: pink
		- `fiber对象`是通过`ReactElement`对象进行创建的, 多个`fiber对象`构成了一棵`fiber树`, `fiber树`是构造`DOM树`的数据模型, `fiber树`的任何改动, 最后都体现到`DOM树`.
	- ### 3. [DOM 对象](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model): 文档对象模型
	  background-color:: pink
		- `DOM`将文档解析为一个由节点和对象（包含属性和方法的对象）组成的结构集合, 也就是常说的`DOM树`.
		- `JavaScript`可以访问和操作存储在 DOM 中的内容, 也就是操作`DOM对象`, 进而触发 UI 渲染.
	- ![image.png](../assets/image_1699607003527_0.png)
	- [[#red]]==注意:==
		- 开发人员能够控制的是`JSX`, 也就是`ReactElement`对象.
		- `fiber树`是通过`ReactElement`生成的, 如果脱离了`ReactElement`,`fiber树`也无从谈起. 所以是`ReactElement`树(不是严格的树结构, 为了方便也称为树)驱动`fiber树`.
		- `fiber树`是`DOM树`的数据模型, `fiber树`驱动`DOM树`
- ## 全局变量
	- 在`React`运行时, `ReactFiberWorkLoop.js`闭包中的`全局变量`会随着`fiber树构造循环`的进行而变化, 现在查看其中重要的全局变量([源码链接](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L247-L367)):
		- ```js
		  // 当前React的执行栈(执行上下文)
		  let executionContext: ExecutionContext = NoContext;
		  
		  // 当前root节点
		  let workInProgressRoot: FiberRoot | null = null;
		  // 正在处理中的fiber节点
		  let workInProgress: Fiber | null = null;
		  // 正在渲染的车道(复数)
		  let workInProgressRootRenderLanes: Lanes = NoLanes;
		  
		  // 包含所有子节点的优先级, 是workInProgressRootRenderLanes的超集
		  // 大多数情况下: 在工作循环整体层面会使用workInProgressRootRenderLanes, 在begin/complete阶段层面会使用 subtreeRenderLanes
		  let subtreeRenderLanes: Lanes = NoLanes;
		  // 一个栈结构: 专门存储当前节点的 subtreeRenderLanes
		  const subtreeRenderLanesCursor: StackCursor<Lanes> = createCursor(NoLanes);
		  
		  // fiber构造完后, root节点的状态: completed, errored, suspended等
		  let workInProgressRootExitStatus: RootExitStatus = RootIncomplete;
		  // 重大错误
		  let workInProgressRootFatalError: mixed = null;
		  // 整个render期间所使用到的所有lanes
		  let workInProgressRootIncludedLanes: Lanes = NoLanes;
		  // 在render期间被跳过(由于优先级不够)的lanes: 只包括未处理的updates, 不包括被复用的fiber节点
		  let workInProgressRootSkippedLanes: Lanes = NoLanes;
		  // 在render期间被修改过的lanes
		  let workInProgressRootUpdatedLanes: Lanes = NoLanes;
		  
		  // 防止无限循环和嵌套更新
		  const NESTED_UPDATE_LIMIT = 50;
		  let nestedUpdateCount: number = 0;
		  let rootWithNestedUpdates: FiberRoot | null = null;
		  
		  const NESTED_PASSIVE_UPDATE_LIMIT = 50;
		  let nestedPassiveUpdateCount: number = 0;
		  
		  // 发起更新的时间
		  let currentEventTime: number = NoTimestamp;
		  let currentEventWipLanes: Lanes = NoLanes;
		  let currentEventPendingLanes: Lanes = NoLanes;
		  ```
	- ### 执行上下文
	  background-color:: pink
		- 在全局变量中有`executionContext`, 代表`渲染期间`的`执行栈`(或叫做`执行上下文`), 它也是一个二进制表示的变量, 通过位运算进行操作(参考[React 算法之位运算](https://7km.top/algorithm/bitfield)). 在源码中一共定义了 8 种执行栈:
			- ```js
			  type ExecutionContext = number;
			  export const NoContext = /*             */ 0b0000000;
			  const BatchedContext = /*               */ 0b0000001;
			  const EventContext = /*                 */ 0b0000010;
			  const DiscreteEventContext = /*         */ 0b0000100;
			  const LegacyUnbatchedContext = /*       */ 0b0001000;
			  const RenderContext = /*                */ 0b0010000;
			  const CommitContext = /*                */ 0b0100000;
			  ```
		- `reconciler 运作流程`的 4 个阶段, 这 4 个阶段只是一个整体划分. 如果具体到每一次更新, 是有差异的. 比如说: [[#red]]==`Legacy`模式下的首次更新, 不会经过`调度中心`(第 2 阶段),而是直接进入`fiber树构造`(第 3 阶段).==
		- 事实上正是`executionContext`在操控`reconciler 运作流程`(源码体现在[scheduleUpdateOnFiber 函数](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L517-L619)).
			- ```js
			  export function scheduleUpdateOnFiber(
			    fiber: Fiber,
			    lane: Lane,
			    eventTime: number,
			  ) {
			    if (lane === SyncLane) {
			      // legacy或blocking模式
			      if (
			        (executionContext & LegacyUnbatchedContext) !== NoContext &&
			        (executionContext & (RenderContext | CommitContext)) === NoContext
			      ) {
			        performSyncWorkOnRoot(root);
			      } else {
			        // 后续的更新
			        // 进入第2阶段, 注册调度任务
			        ensureRootIsScheduled(root, eventTime);
			        if (executionContext === NoContext) {
			          // 如果执行上下文为空, 会取消调度任务, 手动执行回调
			          // 进入第3阶段, 进行fiber树构造
			          flushSyncCallbackQueue();
			        }
			      }
			    } else {
			      // concurrent模式
			      // 无论是否初次更新, 都正常进入第2阶段, 注册调度任务
			      ensureRootIsScheduled(root, eventTime);
			    }
			  }
			  ```
		- >> 在 render 过程中, 每一个阶段都会改变`executionContext`(render 之前, 会设置`executionContext |= RenderContext`; commit 之前, 会设置`executionContext |= CommitContext`), 假设在`render`过程中再次发起更新(如在`UNSAFE_componentWillReceiveProps`生命周期中调用`setState`)则可通过`executionContext`来判断当前的`render`状态.
	- ### 双缓冲技术(double buffering)
	  background-color:: pink
		- `ReactElement, Fiber, DOM三者的关系`, `fiber树`的构造过程, 就是把`ReactElement`转换成`fiber树`的过程. 在这个过程中, 内存里会同时存在 2 棵`fiber树`:
			- 代表**当前**界面的`fiber`树(已经被展示出来, 挂载到`fiberRoot.current`上). 如果是初次构造(`初始化渲染`), 页面还没有渲染, 此时界面对应的 fiber 树为空(`fiberRoot.current = null`).
			  logseq.order-list-type:: number
			- 正在构造的`fiber`树(即将展示出来, 挂载到`HostRootFiber.alternate`上, 正在[[#green]]==构造的节点称为`workInProgress`==). 当构造完成之后, 重新渲染页面, 最后切换`fiberRoot.current = workInProgress`, 使得`fiberRoot.current`重新指向代表当前界面的`fiber`树.
			  logseq.order-list-type:: number
		- 此处涉及到 2 个全局对象`fiberRoot`和`HostRootFiber`, 在[React 应用的启动过程](https://7km.top/main/bootstrap)中有详细的说明.
		- 用图来表述`double buffering`的概念如下:
			- 构造过程中, `fiberRoot.current`指向当前界面对应的`fiber`树.
			  logseq.order-list-type:: number
				- ![image.png](../assets/image_1699953192569_0.png)
			- 构造完成并渲染, 切换`fiberRoot.current`指针, 使其继续指向当前界面对应的`fiber`树(原来代表界面的 fiber 树, 变成了内存中).
			  logseq.order-list-type:: number
				- ![image.png](../assets/image_1699955072953_0.png)
	- ### 优先级 {\#lanes}
		-