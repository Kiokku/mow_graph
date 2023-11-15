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
	- 在进入`react-reconciler`包之前(调用`updateContainer`之前), 内存状态图如下:
	- ![image.png](../assets/image_1699973560836_0.png)
	- 根据这个结构, 可以在控制台中打出当前页面对应的`fiber`树(用于观察其结构):
	-
-