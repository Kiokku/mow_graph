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
		- 上面的代码基本实现了Promise构造函数的主体，但目前还有两个问题：
			- 1. 我们给executor函数传了两个参数：resolve和reject，这[[#red]]==两个参数目前还没有定义。==
			- 2. executor有可能会出错（throw），类似下面这样，而如果executor出错，Promise应该被其throw出的值reject：
				- ```
				  new Promise(function(resolve, reject) {
				    throw 2
				  })
				  ```
		- 所以我们需要在构造函数里定义`resolve`和`reject`这两个函数：
		- ```
		  function Promise(executor) {
		    var self = this
		    self.status = 'pending' // Promise当前的状态
		    self.data = undefined  // Promise的值
		    self.onResolvedCallback = [] // Promise resolve时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
		    self.onRejectedCallback = [] // Promise reject时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
		  
		    function resolve(value) {
		      // TODO
		    }
		  
		    function reject(reason) {
		      // TODO
		    }
		  
		    try { // 考虑到执行executor的过程中有可能出错，所以我们用try/catch块给包起来，并且在出错后以catch到的值reject掉这个Promise
		      executor(resolve, reject) // 执行executor
		    } catch(e) {
		      reject(e)
		    }
		  }
		  ```
		- [[#red]]==**我们这里的实现并没有考虑隐藏this上的变量，这使得这个Promise的状态可以在executor函数外部被改变，在一个靠谱的实现里，构造出的Promise对象的状态和最终结果应当是无法从外部更改的。**==
		- 接下来，我们实现`resolve`和`reject`这两个函数：
		- ```
		  function Promise(executor) {
		    // ...
		  
		    function resolve(value) {
		      if (self.status === 'pending') {
		        self.status = 'resolved'
		        self.data = value
		        for(var i = 0; i < self.onResolvedCallback.length; i++) {
		          self.onResolvedCallback[i](value)
		        }
		      }
		    }
		  
		    function reject(reason) {
		      if (self.status === 'pending') {
		        self.status = 'rejected'
		        self.data = reason
		        for(var i = 0; i < self.onRejectedCallback.length; i++) {
		          self.onRejectedCallback[i](reason)
		        }
		      }
		    }
		  
		    // ...
		  }
		  ```
		- 基本上就是在判断状态为pending之后把状态改为相应的值，并把对应的value和reason存在self的data属性上面，之后执行相应的回调函数.
	- ### `then` 方法
	  background-color:: green
		- Promise对象有一个then方法，用来注册在这个Promise状态确定后的回调，很明显，then方法需要写在**原型链**上。
		- then方法会返回一个Promise，关于这一点，Promise/A+标准并没有要求返回的这个Promise是一个新的对象，但在Promise/A标准中，明确规定了then要返回一个新的对象。
		- 目前的Promise实现中then几乎都是返回一个新的Promise([详情](https://promisesaplus.com/differences-from-promises-a#point-5))对象，所以在我们的实现中，也让then返回一个新的Promise对象。
		- 另外每个Promise对象都可以在其上多次调用then方法，而每次调用then返回的Promise的状态取决于那一次调用then时传入参数的返回值，所以[[#red]]==then不能返回this，因为then每次返回的Promise的结果都有可能不同。==
		- 下面我们来实现then方法：
		- ```
		  // then方法接收两个参数，onResolved，onRejected，分别为Promise成功或失败后的回调
		  Promise.prototype.then = function(onResolved, onRejected) {
		    var self = this
		    var promise2
		  
		    // 根据标准，如果then的参数不是function，则我们需要忽略它，此处以如下方式处理
		    onResolved = typeof onResolved === 'function' ? onResolved : function(v) {}
		    onRejected = typeof onRejected === 'function' ? onRejected : function(r) {}
		  
		    if (self.status === 'resolved') {
		      return promise2 = new Promise(function(resolve, reject) {
		  
		      })
		    }
		  
		    if (self.status === 'rejected') {
		      return promise2 = new Promise(function(resolve, reject) {
		  
		      })
		    }
		  
		    if (self.status === 'pending') {
		      return promise2 = new Promise(function(resolve, reject) {
		  
		      })
		    }
		  }
		  ```
		- Promise总共有三种可能的状态，我们分三个if块来处理，在里面分别都返回一个new Promise。
		- 我们需要在then里面执行onResolved或者onRejected，并根据返回值(标准中记为x)来确定promise2的结果，并且，如果onResolved/onRejected返回的是一个Promise，promise2将直接取这个Promise的结果：
		- ```
		  Promise.prototype.then = function(onResolved, onRejected) {
		    var self = this
		    var promise2
		  
		    // 根据标准，如果then的参数不是function，则我们需要忽略它，此处以如下方式处理
		    onResolved = typeof onResolved === 'function' ? onResolved : function(value) {}
		    onRejected = typeof onRejected === 'function' ? onRejected : function(reason) {}
		  
		    if (self.status === 'resolved') {
		      // 如果promise1(此处即为this/self)的状态已经确定并且是resolved，我们调用onResolved
		      // 因为考虑到有可能throw，所以我们将其包在try/catch块里
		      return promise2 = new Promise(function(resolve, reject) {
		        try {
		          var x = onResolved(self.data)
		          if (x instanceof Promise) { // 如果onResolved的返回值是一个Promise对象，直接取它的结果做为promise2的结果
		            x.then(resolve, reject)
		          }
		          resolve(x) // 否则，以它的返回值做为promise2的结果
		        } catch (e) {
		          reject(e) // 如果出错，以捕获到的错误做为promise2的结果
		        }
		      })
		    }
		  
		    // 此处与前一个if块的逻辑几乎相同，区别在于所调用的是onRejected函数，就不再做过多解释
		    if (self.status === 'rejected') {
		      return promise2 = new Promise(function(resolve, reject) {
		        try {
		          var x = onRejected(self.data)
		          if (x instanceof Promise) {
		            x.then(resolve, reject)
		          }
		        } catch (e) {
		          reject(e)
		        }
		      })
		    }
		  
		    if (self.status === 'pending') {
		    // 如果当前的Promise还处于pending状态，我们并不能确定调用onResolved还是onRejected，
		    // 只能等到Promise的状态确定后，才能确实如何处理。
		    // 所以我们需要把我们的**两种情况**的处理逻辑做为callback放入promise1(此处即this/self)的回调数组里
		    // 逻辑本身跟第一个if块内的几乎一致，此处不做过多解释
		      return promise2 = new Promise(function(resolve, reject) {
		        self.onResolvedCallback.push(function(value) {
		          try {
		            var x = onResolved(self.data)
		            if (x instanceof Promise) {
		              x.then(resolve, reject)
		            }
		          } catch (e) {
		            reject(e)
		          }
		        })
		  
		        self.onRejectedCallback.push(function(reason) {
		          try {
		            var x = onRejected(self.data)
		            if (x instanceof Promise) {
		              x.then(resolve, reject)
		            }
		          } catch (e) {
		            reject(e)
		          }
		        })
		      })
		    }
		  }
		  
		  // 为了下文方便，我们顺便实现一个catch方法
		  Promise.prototype.catch = function(onRejected) {
		    return this.then(null, onRejected)
		  }
		  ```
		- 至此，我们基本实现了Promise标准中所涉及到的内容，但还有几个问题：
			- 1. 不同的Promise实现之间需要无缝的可交互，即Q的Promise，ES6的Promise，和我们实现的Promise之间以及其它的Promise实现，应该并且是有必要无缝相互调用的，比如：
			- ```
			  // 此处用MyPromise来代表我们实现的Promise
			  new MyPromise(function(resolve, reject) { // 我们实现的Promise
			    setTimeout(function() {
			      resolve(42)
			    }, 2000)
			  }).then(function() {
			    return new Promise.reject(2) // ES6的Promise
			  }).then(function() {
			    return Q.all([ // Q的Promise
			      new MyPromise(resolve=>resolve(8)), // 我们实现的Promise
			      new Promise.resolve(9), // ES6的Promise
			      Q.resolve(9) // Q的Promise
			    ])
			  })
			  ```
			- 我们前面实现的代码并没有处理这样的逻辑，我们[[#red]]==只判断了onResolved/onRejected的返回值是否为我们实现的Promise的实例，并没有做任何其它的判断，所以上面这样的代码目前是没有办法在我们的Promise里正确运行的。==
			- 2. 下面这样的代码目前也是没办法处理的：
			- ```
			  new Promise(resolve=>resolve(8))
			    .then()
			    .then()
			    .then(function foo(value) {
			      alert(value)
			    })
			  ```
			- 正确的行为应该是alert出8，而如果拿我们的Promise，运行上述代码，将会alert出undefined。这种行为称为**穿透**，即8这个值会穿透两个then(说Promise更为准确)到达最后一个then里的foo函数里，成为它的实参，最终将会alert出8。
		- #### Promise值的穿透
		  background-color:: purple
			-