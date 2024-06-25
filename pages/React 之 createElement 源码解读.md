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
		- ```
		  const childrenLength = arguments.length - 2;
		    if (childrenLength === 1) {
		      props.children = children;
		    } else if (childrenLength > 1) {
		      const childArray = Array(childrenLength);
		      for (let i = 0; i < childrenLength; i++) {
		        childArray[i] = arguments[i + 2];
		      }
		      props.children = childArray;
		  }
		  ```
	- ### 第四段代码 defaultProps
	  background-color:: pink
		- 处理组件的`defaultProps`，无论是函数组件还是类组件都支持 `defaultProps`
	- ### 第五段代码 owner
	  background-color:: pink
		- ```
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
		  
		  ```
		- [[#blue]]==`ReactCurrentOwner.current`==： 就是指向处于构建过程中的组件的 owner，具体作用在以后的文章中还有介绍，现在可以简单的理解为，它就是用于**记录临时变量**。
- ## ReactElement
  collapsed:: true
	- ### 源码
	  background-color:: pink
		- ```
		  const ReactElement = function(type, key, ref, self, source, owner, props) {
		    const element = {
		      // This tag allows us to uniquely identify this as a React Element
		      $$typeof: REACT_ELEMENT_TYPE,
		  
		      // Built-in properties that belong on the element
		      type: type,
		      key: key,
		      ref: ref,
		      props: props,
		  
		      // Record the component responsible for creating this element.
		      _owner: owner,
		    };
		  
		    return element;
		  };
		  
		  ```
	- ### REACT_ELEMENT_TYPE
	  background-color:: pink
		- ```
		  export const REACT_ELEMENT_TYPE = Symbol.for('react.element');
		  ```
		- 它就是一个唯一常量值，用于标示是 React 元素节点
		- `$$typeof`：主要是为了处理 web 安全问题，试想这样一段代码：
			- ```
			  let message = { text: expectedTextButGotJSON };
			  
			  // React 0.13 中有风险
			  <p>
			    {message.text}
			  </p>
			  ```
			- 如果 `expectedTextButGotJSON`是来自于服务器的值，比如：
			- ```
			  // 服务端允许用户存储 JSON
			  let expectedTextButGotJSON = {
			    type: 'div',
			    props: {
			      dangerouslySetInnerHTML: {
			        __html: '/* something bad */'
			      },
			    },
			    // ...
			  };
			  let message = { text: expectedTextButGotJSON };
			  ```
			- 这就很容易受到 XSS 攻击，虽然这个攻击是来自服务器端的漏洞，但使用 React 我们可以处理的更好。如果我们用 Symbol 标记每个 React 元素，因为服务端的数据不会有 `Symbol.for('react.element')`，[[#green]]==React 就可以检测 `element.$$typeof`，如果元素丢失或者无效，则可以拒绝处理该元素，==这样就保证了安全性。