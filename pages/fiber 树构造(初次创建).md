- 示例代码如下([codesandbox 地址](https://codesandbox.io/s/busy-jang-b26hy?file=/src/App.js)):
- ```js
  class App extends React.Component {
    componentDidMount() {
      console.log(`App Mount`);
      console.log(`App 组件对应的fiber节点: `, this._reactInternals);
    }
    render() {
      return (
        <div className="app">
          <header>header</header>
          <Content />
        </div>
      );
    }
  }
  
  class Content extends React.Component {
    componentDidMount() {
      console.log(`Content Mount`);
      console.log(`Content 组件对应的fiber节点: `, this._reactInternals);
    }
    render() {
      return (
        <React.Fragment>
          <p>1</p>
          <p>2</p>
        </React.Fragment>
      );
    }
  }
  export default App;
  ```
- ## 启动阶段
	- 在进入`react-reconciler`包之前([[#green]]==调用`updateContainer`==之前), 内存状态图如下:
	- ![image.png](../assets/image_1699973560836_0.png)
	- 根据这个结构, 可以在控制台中打出当前页面对应的`fiber`树(用于观察其结构):
		- `document.getElementById('root')._reactRootContainer._internalRoot.current;`
	- 然后进入`react-reconciler`包调用[updateContainer 函数](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberReconciler.old.js#L250-L321):
		- ```js
		  // ... 省略了部分代码
		  export function updateContainer(
		    element: ReactNodeList,
		    container: OpaqueRoot,
		    parentComponent: ?React$Component<any, any>,
		    callback: ?Function,
		  ): Lane {
		    // 获取当前时间戳
		    const current = container.current;
		    const eventTime = requestEventTime();
		    // 1. 创建一个优先级变量(车道模型)
		    const lane = requestUpdateLane(current);
		  
		    // 2. 根据车道优先级, 创建update对象, 并加入fiber.updateQueue.pending队列
		    const update = createUpdate(eventTime, lane);
		    update.payload = { element };
		    callback = callback === undefined ? null : callback;
		    if (callback !== null) {
		      update.callback = callback;
		    }
		    enqueueUpdate(current, update);
		  
		    // 3. 进入reconciler运作流程中的`输入`环节
		    scheduleUpdateOnFiber(current, lane, eventTime);
		    return lane;
		  }
		  ```
		-