- > https://react.iamkasong.com/process/fiber-mental.html
-
- ## 什么是代数效应
	- `代数效应`是`函数式编程`中的一个概念，用于将`副作用`从`函数`调用中分离。
	- 假设我们有一个函数`getTotalPicNum`，传入2个`用户名称`后，分别查找该用户在平台保存的图片数量，最后将图片数量相加后返回。
	- ```
	  function getTotalPicNum(user1, user2) {
	    const picNum1 = getPicNum(user1);
	    const picNum2 = getPicNum(user2);
	  
	    return picNum1 + picNum2;
	  }
	  ```
	- 在`getTotalPicNum`中，我们不关注`getPicNum`的实现，只在乎“获取到两个数字后将他们相加的结果返回”这一过程。
	- 接下来我们来实现`getPicNum`。
	- "用户在平台保存的图片数量"是保存在服务器中的。所以，为了获取该值，我们需要发起[[#blue]]==异步请求==。
	- ```
	  async function getTotalPicNum(user1, user2) {
	    const picNum1 = await getPicNum(user1);
	    const picNum2 = await getPicNum(user2);
	  
	    return picNum1 + picNum2;
	  }
	  ```
	- 但是，`async await`是有`传染性`的 —— 当一个函数变为`async`后，这意味着调用他的函数也需要是`async`，这破坏了`getTotalPicNum`的同步特性。
	- 我们[[#red]]==虚构==一个类似`try...catch`的语法 —— `try...handle`与两个操作符`perform`、`resume`。
	- ```
	  function getPicNum(name) {
	    const picNum = perform name;
	    return picNum;
	  }
	  
	  try {
	    getTotalPicNum('kaSong', 'xiaoMing');
	  } handle (who) {
	    switch (who) {
	      case 'kaSong':
	        resume with 230;
	      case 'xiaoMing':
	        resume with 122;
	      default:
	        resume with 0;
	    }
	  }
	  ```
	- 当执行到`getTotalPicNum`内部的`getPicNum`方法时，会执行`perform name`。
	- 此时函数调用栈会从`getPicNum`方法内跳出，被最近一个`try...handle`捕获。类似`throw Error`后被最近一个`try...catch`捕获。
	- 类似`throw Error`后`Error`会作为`catch`的参数，`perform name`后`name`会作为`handle`的参数。
	- 与`try...catch`最大的不同在于：当`Error`被`catch`捕获后，之前的调用栈就销毁了。而`handle`执行`resume`后会回到之前`perform`的调用栈。
	- 对于`case 'kaSong'`，执行完`resume with 230;`后调用栈会回到`getPicNum`，此时`picNum === 230`
	- [[#blue]]==总结一下：==`代数效应`能够将`副作用`（例子中为`请求图片数量`）从函数逻辑中分离，使函数关注点保持纯粹。
	- 并且，从例子中可以看出，`perform resume`不需要区分同步异步。
- ## 代数效应在React中的应用
	- 那么`代数效应`与`React`有什么关系呢？最明显的例子就是`Hooks`。
	- 对于类似`useState`、`useReducer`、`useRef`这样的`Hook`，我们不需要关注`FunctionComponent`的`state`在`Hook`中是如何保存的，`React`会为我们处理。
	- 举例：可以看看官方的[Suspense Demo](https://codesandbox.io/s/frosty-hermann-bztrp?file=/src/index.js:152-160)
		- 在`Demo`中`ProfileDetails`用于展示`用户名称`。而`用户名称`是`异步请求`的。
		- 但是`Demo`中完全是`同步`的写法。
		- ```
		  function ProfileDetails() {
		    const user = resource.user.read();
		    return <h1>{user.name}</h1>;
		  }
		  ```
- ## 代数效应与Generator
	- `异步可中断更新`可以理解为：`更新`在执行过程中可能会被打断（浏览器时间分片用尽或有更高优任务插队），当可以继续执行时恢复之前执行的中间状态。
	- 这就是`代数效应`中`try...handle`的作用。
	- 其实，浏览器原生就支持类似的实现，这就是`Generator`。
	- 但是`Generator`的一些缺陷使`React`团队放弃了他：
		- 1. 类似`async`，`Generator`也是`传染性`的，使用了`Generator`则上下文的其他函数也需要作出改变。这样心智负担比较重。
		- 2. Generator`执行的`中间状态`是上下文关联的。
- ## 代数效应与Fiber
	- `Fiber`并不是计算机术语中的新名词，他的中文翻译叫做`纤程`，与进程（Process）、线程（Thread）、协程（Coroutine）同为程序执行过程。
	- `React Fiber`可以理解为：
		- `React`内部实现的一套状态更新机制。支持任务不同`优先级`，可中断与恢复，并且恢复后可以复用之前的`中间状态`。
		- 其中每个任务更新单元为`React Element`对应的`Fiber节点`。
	-