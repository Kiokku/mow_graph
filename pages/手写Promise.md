- > https://github.com/xieranmaya/blog/issues/3
-
- ## Promise标准解读
	- ### 1. 只有一个`then`方法，没有`catch`，`race`，`all`等方法
	  background-color:: pink
		- Promise标准中仅指定了Promise对象的`then`方法的行为，其它一切我们常见的方法/函数都并没有指定，包括`catch`，`race`，`all`等常用方法，甚至也没有指定该如何构造出一个Promise对象，另外then也没有一般实现中（Q, $q等）所支持的第三个参数，一般称onProgress
	- ### 2. `then`方法返回一个新的Promise
	  background-color:: pink
		- Promise的`then`方法返回一个新的Promise，而不是返回this，此处在下文会有更多解释
		- ```
		  promise2 = promise1.then(alert)
		  promise2 != promise1 // true
		  ```
	- ### 3. 不同Promise的实现需要可以相互调用(interoperable)，可互操作性
	  background-color:: pink
	- ### 4.Promise的初始状态为`pending`，它可以由此状态转换为`fulfilled`（本文为了一致把此状态叫做`resolved`）或者`rejected`，一旦状态确定，就不可以再次转换为其它状态，状态确定的过程称为settle。
	  background-color:: pink
	- [更具体的标准见这里](https://promisesaplus.com/)
- ## 一步一步实现一个Promise
	- ### 构造函数
	  background-color:: green
		- 因为标准并没有指定如何构造一个Promise对象，所以我们同样以目前一般Promise实现中通用的方法来构造一个Promise对象，也是ES6原生Promise里所使用的方式，即：
		- ```
		  // Promise构造函数接收一个executor函数，executor函数执行完同步或异步操作后，调用它的两个参数resolve和reject
		  var promise = new Promise(function(resolve, reject) {
		    /*
		      如果操作成功，调用resolve并传入value
		      如果操作失败，调用reject并传入reason
		    */
		  })
		  ```
		- 我们先实现构造函数的框架如下：
		- ```
		  function Promise(executor) {
		    var self = this
		    self.status = 'pending' // Promise当前的状态
		    self.data = undefined  // Promise的值
		    self.onResolvedCallback = [] // Promise resolve时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
		    self.onRejectedCallback = [] // Promise reject时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
		  
		    executor(resolve, reject) // 执行executor并传入相应的参数
		  }
		  ```