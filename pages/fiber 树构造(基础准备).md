- > 在 React 运行时中, `fiber树构造`位于`react-reconciler`包.
  >
  > 1. 从`scheduler`调度中心的角度来看, 它是任务队列`taskQueue`中的一个具体的任务回调(`task.callback`).
  > 2. 从[React 工作循环](https://7km.top/main/workloop)的角度来看, 它属于`fiber树构造循环`.
- ## ReactElement, Fiber, DOM 三者的关系
	- ### 1. [ReactElement 对象](https://github.com/facebook/react/blob/v17.0.2/packages/react/src/ReactElement.js#L126-L146)(type 定义在[shared 包中](https://github.com/facebook/react/blob/v17.0.2/packages/shared/ReactElementType.js#L15))
	  background-color:: pink
		-
-