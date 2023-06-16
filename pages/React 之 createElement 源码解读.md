- > https://juejin.cn/post/7160981608885927972
-
- ## createElement
	- ### 源码
	  background-color:: pink
		- `PATH：src/react/packages/react/src/ReactElement.js`
		- ```
		  // 简化后
		  const RESERVED_PROPS = {
		    key: true,
		    ref: true,
		    __self: true,
		    __source: true,
		  };
		  
		  export function createElement(type, config, ...children) {
		    let propName;
		  
		    // Reserved names are extracted
		    const props = {};
		  
		    // 第一段
		    let key = '' + config.key;
		    let ref = config.ref;
		    let self = config.__self;
		    let source = config.__source;
		  
		    // 第二段
		    for (propName in config) {
		      if (config.hasOwnProperty(propName) && !RESERVED_PROPS.hasOwnProperty(propName)) {
		        props[propName] = config[propName];
		      }
		    }
		  
		    // 第三段
		    props.children = children;
		  
		    // 第四段
		    if (type && type.defaultProps) {
		      const defaultProps = type.defaultProps;
		      for (propName in defaultProps) {
		        if (props[propName] === undefined) {
		          props[propName] = defaultProps[propName];
		        }
		      }
		    }
		  
		    // 第五段
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
		  
		  ```
	- ### 函数入参
	  background-color:: pink
		- [[#blue]]==`type` 表示类型==：既可以是标签名字符串（如 div 或 span），也可以是 React 组件（如 Foo）
		- [[#blue]]==`config` 表示传入的属性==
		- [[#blue]]==`children` 表示子元素==
	- ### 第一段代码 __self 和 __source
	  background-color:: pink
		- ```
		    // 第一段
		    let key = '' + config.key;
		    let ref = config.ref;
		    let self = config.__self;
		    let source = config.__source;
		  ```
		- 通过这个 [issue](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Falangpierce%2Fsucrase%2Fissues%2F232)，我们了解到，`__self` 和 `__source` 是 `babel-preset-react`注入的调试信息，可以提供更有用的错误信息。
		- [babel-preset-react 的文档](https://link.juejin.cn/?target=https%3A%2F%2Fwww.babeljs.cn%2Fdocs%2Fbabel-preset-react)
			- > **development**
			  > boolean 类型，默认值为 false. 这可以用于开启特定于开发环境的某些行为，例如添加 __source 和 __self。 在与 env 参数 配置或 js 配置文件 配合使用时，此功能很有用。
	- ### 第二段代码 props 对象
	  background-color:: pink
		- ```
		  // 第二段
		  for (propName in config) {
		      if (config.hasOwnProperty(propName) && !RESERVED_PROPS.hasOwnProperty(propName)) {
		        props[propName] = config[propName];
		      }
		  }
		  ```
		- 构建一个 props 对象，去除传入的 `key`、`ref`、`__self`、`__source`属性
		- [[#red]]==这就是为什么在组件中，我们明明传入了 `key` 和`ref`，但我们无法通过 `this.props.key` 或者 `this.props.ref` 来获取传入的值，就是因为在这里被去除掉了。==
	- ### 第三段代码 children
	  background-color:: pink
		-