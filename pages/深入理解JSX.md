- > https://react.iamkasong.com/preparation/jsx.html
-
- > `JSX`和`Fiber节点`是同一个东西么？
  > `React Component`、`React Element`是同一个东西么，他们和`JSX`有什么关系？
- ## JSX简介
	- `JSX`在编译时会被`Babel`编译为`React.createElement`方法。
	- `<div id="foo">bar</div>`
	- Babel 会将其转译为：
	- `React.createElement("div", {id: "foo"}, "bar");`
	- > **注意**
	  > 在React17中，已经不需要显式导入React了。详见[介绍全新的 JSX 转换](https://zh-hans.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html)
	- `JSX`并不是只能被编译为`React.createElement`方法，你可以通过[@babel/plugin-transform-react-jsx](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx)插件显式告诉`Babel`编译时需要将`JSX`编译为什么函数的调用（默认为`React.createElement`）。
- ## [React.createElement](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react/src/ReactElement.js#L348)
	- ```
	  export function createElement(type, config, children) {
	    let propName;
	  
	    const props = {};
	  
	    let key = null;
	    let ref = null;
	    let self = null;
	    let source = null;
	  
	    if (config != null) {
	      // 将 config 处理后赋值给 props
	      // ...省略
	    }
	  
	    const childrenLength = arguments.length - 2;
	    // 处理 children，会被赋值给props.children
	    // ...省略
	  
	    // 处理 defaultProps
	    // ...省略
	  
	    return ReactElement(
	      type,
	      key,
	      ref,
	      self,
	      source,
	      ReactCurrentOwner.current,
	      props,
	    );
	  }
	  
	  const ReactElement = function(type, key, ref, self, source, owner, props) {
	    const element = {
	      // 标记这是个 React Element
	      $$typeof: REACT_ELEMENT_TYPE,
	  
	      type: type,
	      key: key,
	      ref: ref,
	      props: props,
	      _owner: owner,
	    };
	  
	    return element;
	  };
	  ```
	-