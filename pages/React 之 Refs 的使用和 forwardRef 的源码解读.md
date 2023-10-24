- > https://juejin.cn/post/7161719602652086308
- ## 三种使用方式
	- React 提供了 Refs，帮助我们访问 DOM 节点或在 render 方法中创建的 React 元素。
	- React 提供了三种使用 Ref 的方式：
	- ### 1. String Refs
		- *ref 直接传入一个字符串*
		- *通过 this.refs.xxx 获取 DOM 节点*
		- ```
		  class App extends React.Component {
		      constructor(props) {
		          super(props)
		      }
		      componentDidMount() {
		          setTimeout(() => {
		               // 2. 通过 this.refs.xxx 获取 DOM 节点
		               this.refs.textInput.value = 'new value'
		          }, 2000)
		      }
		      render() {
		          // 1. ref 直接传入一个字符串
		          return (
		              <div>
		                <input ref="textInput" value='value' />
		              </div>
		          )
		      }
		  }
		  
		  root.render(<App />);
		  ```
	- ### 2. 回调 Refs
		- *ref 传入一个回调函数*
		- *通过实例属性获取 DOM 节点*
		- ```
		  class App extends React.Component {
		      constructor(props) {
		          super(props)
		      }
		      componentDidMount() {
		          setTimeout(() => {
		                // 2. 通过实例属性获取 DOM 节点
		                this.textInput.value = 'new value'
		          }, 2000)
		      }
		      render() {
		          // 1. ref 传入一个回调函数
		          // 该函数中接受 React 组件实例或 DOM 元素作为参数
		          // 我们通常会将其存储到具体的实例属性（this.textInput）
		          return (
		              <div>
		                <input ref={(element) => {
		                  this.textInput = element;
		                }} value='value' />
		              </div>
		          )
		      }
		  }
		  
		  root.render(<App />);
		  ```
	- ### 3. createRef
	  id:: 653792f7-f740-4822-b09e-3edea920ee0b
		- *使用 createRef 创建 Refs*
		- *通过 ref 属性附加到 React 元素*
		- *通过 Refs 的 current 属性进行引用*
		- ```
		  class App extends React.Component {
		      constructor(props) {
		          super(props)
		          // 1. 使用 createRef 创建 Refs
		          // 并将 Refs 分配给实例属性 textInputRef，以便在整个组件中引用
		          this.textInputRef = React.createRef();
		      }
		      componentDidMount() {
		          setTimeout(() => {
		              // 3. 通过 Refs 的 current 属性进行引用
		              this.textInputRef.current.value = 'new value'
		          }, 2000)
		      }
		      render() {
		          // 2. 通过 ref 属性附加到 React 元素
		          return (
		              <div>
		                <input ref={this.textInputRef} value='value' />
		              </div>
		          )
		      }
		  }
		  ```
		- 这是最被推荐使用的方式。
- ## 两种使用目的
	- Refs 除了用于获取具体的 DOM 节点外，也可以获取 [[#green]]==Class 组件==的实例，当获取到实例后，可以调用其中的方法，从而强制执行，比如动画之类的效果。
- ## forwardRef
	- [[#red]]==我们不能在函数组件上使用 ref 属性，因为函数组件没有实例。==
	- 如果我们强行使用，React 会报错并提示我们用 `forwardRef`。
	- 但是，对于“获取组件实例，调用实例方法”这个需求，即使使用 forwardRef 也做不到，借助 forwardRef 后，我们也就是跟类组件一样，可以在组件上使用 ref 属性，然后将 ref 绑定到具体的 DOM 元素或者 class 组件上，也就是我们常说的 Refs 转发。
- ## Refs 转发
	- 有的时候，我们开发一个组件，这个组件需要对组件使用者提供一个 ref 属性，用于让组件使用者获取具体的 DOM 元素，我们就需要进行 Refs 转发，我们通常的做法是：
	- ```
	  // 类组件
	  class Child extends React.Component {
	      render() {
	          const {inputRef, ...rest} = this.props;
	          // 3. 这里将 props 中的 inputRef 赋给 DOM 元素的 ref
	          return <input ref={inputRef} {...rest} placeholder="value" />
	      }
	  }
	  // 函数组件
	  function Child(props) {
	        const {inputRef, ...rest} = props;
	        // 3. 这里将 props 中的 inputRef 赋给 DOM 元素的 ref
	        return <input ref={inputRef} {...rest} placeholder="value" />
	  }
	  
	  class Parent extends React.Component {
	      constructor(props) {
	          super(props)
	          // 1. 创建 refs
	          this.inputRef = React.createRef();
	      }
	      componentDidMount() {
	          setTimeout(() => {
	              // 4. 使用 this.inputRef.current 获取子组件中渲染的 DOM 节点
	              this.inputRef.current.value = 'new value'
	          }, 2000)
	      }
	      render() {
	          // 2. 因为 ref 属性不能通过 this.props 获取，所以这里换了一个属性名
	          return <Child inputRef={this.inputRef} />
	      }
	  }
	  ```
	- React 提供了 forwardRef 这个 API，我们直接看使用示例：
	- ```
	  // 3. 子组件通过 forwardRef 获取 ref，并通过 ref 属性绑定 React 元素
	  const Child = forwardRef((props, ref) => (
	    <input ref={ref} placeholder="value" />
	  ));
	  
	  class Parent extends React.Component {
	      constructor(props) {
	          super(props)
	          // 1. 创建 refs
	          this.inputRef = React.createRef();
	      }
	      componentDidMount() {
	          setTimeout(() => {
	              // 4. 使用 this.inputRef.current 获取子组件中渲染的 DOM 节点
	              this.inputRef.current.value = 'new value'
	          }, 2000)
	      }
	      render() {
	          // 2. 传给子组件的 ref 属性
	          return <Child ref={this.inputRef} />
	      }
	  }
	  ```
	-