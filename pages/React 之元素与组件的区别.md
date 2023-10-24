- > https://juejin.cn/post/7161320926728945701
- ## 从问题出发
  collapsed:: true
	- 想要实现一个 `useTitle` 方法，具体使用示例如下：
	- ```
	  function Header() {
	      const [Title, changeTitle] = useTitle();
	      return (
	          <div onClick={() => changeTitle('new title')}>
	            <Title />
	          </div>
	      )
	  }
	  
	  ```
	- 但在编写 `useTitle` 代码的时候却出了问题：
	- ```
	  function TitleComponent({title}) {
	      return <div>{title}</div>
	  }
	  
	  function useTitle() {
	      const [title, changeTitle] = useState('default title');
	  
	      const Element = React.createElement(TitleComponent, {title});
	      return [Element.type, changeTitle];
	  }
	  
	  ```
	- 这段代码直接报错，连渲染都渲染不出来。
- ## 元素与组件
  collapsed:: true
	- ### 元素
		- Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用。以下两种示例代码完全等效：
		- ```
		  const element = <h1 className="greeting">Hello, world!</h1>;
		  
		  const element = React.createElement(
		    'h1',
		    {className: 'greeting'},
		    'Hello, world!'
		  );
		  
		  ```
		- `React.createElement()` 会预先执行一些检查，以帮助你编写无错代码，但实际上它创建了一个这样的对象：
		- ```
		  // 注意：这是简化过的结构
		  const element = {
		    type: 'h1',
		    props: {
		      className: 'greeting',
		      children: 'Hello, world!'
		    }
		  };
		  
		  ```
		- 这些对象被称为 “[[$blue]]==React 元素==”。它们描述了你希望在屏幕上看到的内容。
		- React 元素其实就是指我们日常编写的 JSX 代码，它会被 Babel 转义为一个函数调用，最终得到的结果是一个描述 DOM 结构的对象，它的数据结构本质是一个 JS 对象。
	- ### 组件
		- 组件有两种，函数组件和 class 组件
		- 对于组件，我们要使用类似于 HTML 标签的方式进行调用，Babel 会将其转译为一个函数调用：
		- ```
		  const element = <Welcome name="Sara" />;
		  
		  const element = React.createElement(Welcome, {
		    name: "Sara"
		  });
		  
		  ```
		- 组件的数据结构本质是一个函数或者类，当你使用元素标签的方式进行调用时，函数或者类会被执行，最终返回一个 React 元素。
- ## 问题如何解决
  collapsed:: true
	- 第一种我们返回 React 元素：
		- ```
		  const root = ReactDOM.createRoot(document.getElementById('root'));
		  
		  function Header() {
		      const [Title, changeTitle] = useTitle();
		      // 这里因为返回的是 React 元素，所以我们使用 {} 的方式嵌入表达式
		      return (
		          <div onClick={() => changeTitle('new title')}>
		            {Title}
		          </div>
		      )
		  }
		  
		  function TitleComponent({title}) {
		      return <div>{title}</div>
		  }
		  
		  function useTitle() {
		      const [title, changeTitle] = useState('default title');
		  
		      // createElement 返回的是 React 元素
		      const Element = React.createElement(TitleComponent, {title});
		      return [Element, changeTitle];
		  }
		  
		  root.render(<Header />);
		  
		  ```
	- 第二种我们返回 React 组件：
		- ```
		  
		  const root = ReactDOM.createRoot(document.getElementById('root'));
		  
		  function Header() {
		      const [Title, changeTitle] = useTitle();
		      // 因为返回的是 React 组件，所以我们使用元素标签的方式调用
		      return (
		          <div onClick={() => changeTitle('new title')}>
		            <Title />
		          </div>
		      )
		  }
		  
		  function TitleComponent({title}) {
		      return <div>{title}</div>
		  }
		  
		  function useTitle() {
		      const [title, changeTitle] = useState('default title');
		  
		      // 这里我们构建了一个函数组件
		      const returnComponent = () => {
		      	return <TitleComponent title={title} />
		      }
		      // 这里我们直接将组件返回出去
		      return [returnComponent, changeTitle];
		  }
		  
		  root.render(<Header />);
		  
		  ```
- ## 自定义内容
  collapsed:: true
	- 有的时候我们需要给组件传入一个自定义内容。
	- 举个例子，我们实现了一个 Modal 组件，有确定按钮，有取消按钮，但 Modal 展示的内容为了更加灵活，我们[[#green]]==提供了一个 props 属性，用户可以自定义一个组件传入其中==，用户提供什么，Modal 就展示什么，Modal 相当于一个容器，那么，我们该怎么实现这个功能呢？
	- ### 第一种实现方式
		- ```
		  function Modal({content}) {
		    return (
		      <div>
		        {content}
		        <button>确定</button>
		        <button>取消</button>
		      </div>
		    )
		  }
		  
		  function CustomContent({text}) {
		    return <div>{text}</div>
		  }
		  
		  <Modal content={<CustomContent text="content" />} />
		  
		  ```
		- 根据前面的知识，我们可以知道，`content` 属性这里传入的其实是一个 React 元素，所以 Modal 组件的内部是用 `{}` 进行渲染。
	- ### 第二种实现方式
		- 有的时候，我们可能会用到组件内部的值。
		- 比如一个倒计时组件 `Timer`，依然提供了一个属性 `content`，用于自定义时间的展示样式，时间由 `Timer` 组件内部处理，展示样式则完全由用户自定义，在这种时候，我们就可以选择传入一个组件：
		- ```
		  function Timer({content: Content}) {
		      const [time, changeTime] = useState('0');
		  
		      useEffect(() => {
		          setTimeout(() => {
		              changeTime((new Date).toLocaleTimeString())
		  	}, 1000)
		      }, [time])
		  
		      return (
		          <div>
		            <Content time={time} />
		          </div>
		      )
		  }
		  
		  function CustomContent({time}) {
		      return <div style={{border: '1px solid #ccc'}}>{time}</div>
		  }
		  
		  
		  <Timer content={CustomContent} />
		  
		  ```
		- 在这个示例中，我们可以看到 `content` 属性传入的是一个 React 组件 CustomContent，而 CustomContent 组件会被传入 time 属性，我们正是基于这个约定进行的 CustomContent 组件的开发。
	- ### 第三种实现方式
		- 在面对第二种实现方式的需求时，除了上面这种实现方式，还有一种称为 `render props` 的技巧，比第二种方式更常见一些，我们依然以 Timer 组件为例：
		- ```
		  function Timer({renderContent}) {
		      const [time, changeTime] = useState('0');
		  
		      useEffect(() => {
		          setTimeout(() => {
		              changeTime((new Date).toLocaleTimeString())
		  	}, 1000)
		      }, [time])
		  
		    // 这里直接调用传入的 renderContent 函数
		      return (
		          <div>
		            {renderContent(time)}
		          </div>
		      )
		  }
		  
		  function CustomContent({time}) {
		      return <div style={{border: '1px solid #ccc'}}>{time}</div>
		  }
		  
		  root.render(<Timer renderContent={(time) => {
		      return <CustomContent time={time} />
		  }} />);
		  
		  ```
		- `renderContent` 传入了一个函数，该函数接收 `time` 作为参数，返回一个 React 元素，而在 `Timer` 内部，我们直接执行了 renderContent 函数，并传入内部处理好的 time 参数，由此实现了用户使用组件内部值自定义渲染内容。