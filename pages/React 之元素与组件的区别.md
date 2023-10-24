- > https://juejin.cn/post/7161320926728945701
- ## 元素与组件
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