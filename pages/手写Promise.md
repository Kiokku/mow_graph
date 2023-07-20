- > https://github.com/xieranmaya/blog/issues/3
-
- ## Promise标准解读
  collapsed:: true
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
		  collapsed:: true
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
		  collapsed:: true
			- 通过观察，会发现我们希望下面这段代码：
			- ```
			  new Promise(resolve=>resolve(8))
			    .then()
			    .catch()
			    .then(function(value) {
			      alert(value)
			    })
			  ```
			- 跟下面这段代码的行为是一样的：
			- ```
			  new Promise(resolve=>resolve(8))
			    .then(function(value){
			      return value
			    })
			    .catch(function(reason){
			      throw reason
			    })
			    .then(function(value) {
			      alert(value)
			    })
			  ```
			- 所以如果想要把then的实参留空且让值可以穿透到后面，意味着then的两个参数的[[#blue]]==默认值==分别为`function(value) {return value}`，`function(reason) {throw reason}`。
			  所以我们只需要把then里判断`onResolved`和`onRejected`的部分改成如下即可：
			- ```
			  onResolved = typeof onResolved === 'function' ? onResolved : function(value) {return value}
			  onRejected = typeof onRejected === 'function' ? onRejected : function(reason) {throw reason}
			  ```
		- #### 不同Promise的交互
		  background-color:: purple
			- 关于不同Promise间的交互，其实标准里是有[说明](https://promisesaplus.com/#point-46)的，其中详细指定了如何通过then的实参返回的值来决定promise2的状态，我们只需要按照标准把标准的内容转成代码即可。
			- 这里简单解释一下标准：
			- >我们要把onResolved/onRejected的返回值，x，当成一个可能是Promise的对象，也即标准里所说的thenable，并以最保险的方式调用x上的then方法，如果大家都按照标准实现，那么不同的Promise之间就可以交互了。而标准为了保险起见，即使x返回了一个带有then属性但并不遵循Promise标准的对象（比如说这个x把它then里的两个参数都调用了，同步或者异步调用（PS，原则上then的两个参数需要异步调用，下文会讲到），或者是出错后又调用了它们，或者then根本不是一个函数），也能尽可能正确处理。
			- ```
			  /*
			  resolvePromise函数即为根据x的值来决定promise2的状态的函数
			  也即标准中的[Promise Resolution Procedure](https://promisesaplus.com/#point-47)
			  x为`promise2 = promise1.then(onResolved, onRejected)`里`onResolved/onRejected`的返回值
			  `resolve`和`reject`实际上是`promise2`的`executor`的两个实参，因为很难挂在其它的地方，所以一并传进来。
			  相信各位一定可以对照标准把标准转换成代码，这里就只标出代码在标准中对应的位置，只在必要的地方做一些解释
			  */
			  function resolvePromise(promise2, x, resolve, reject) {
			    var then
			    var thenCalledOrThrow = false
			  
			    if (promise2 === x) { // 对应标准2.3.1节
			      return reject(new TypeError('Chaining cycle detected for promise!'))
			    }
			  
			    if (x instanceof Promise) { // 对应标准2.3.2节
			      // 如果x的状态还没有确定，那么它是有可能被一个thenable决定最终状态和值的
			      // 所以这里需要做一下处理，而不能一概的以为它会被一个“正常”的值resolve
			      if (x.status === 'pending') {
			        x.then(function(value) {
			          resolvePromise(promise2, value, resolve, reject)
			        }, reject)
			      } else { // 但如果这个Promise的状态已经确定了，那么它肯定有一个“正常”的值，而不是一个thenable，所以这里直接取它的状态
			        x.then(resolve, reject)
			      }
			      return
			    }
			  
			    if ((x !== null) && ((typeof x === 'object') || (typeof x === 'function'))) { // 2.3.3
			      try {
			  
			        // 2.3.3.1 因为x.then有可能是一个getter，这种情况下多次读取就有可能产生副作用
			        // 即要判断它的类型，又要调用它，这就是两次读取
			        then = x.then 
			        if (typeof then === 'function') { // 2.3.3.3
			          then.call(x, function rs(y) { // 2.3.3.3.1
			            if (thenCalledOrThrow) return // 2.3.3.3.3 即这三处谁选执行就以谁的结果为准
			            thenCalledOrThrow = true
			            return resolvePromise(promise2, y, resolve, reject) // 2.3.3.3.1
			          }, function rj(r) { // 2.3.3.3.2
			            if (thenCalledOrThrow) return // 2.3.3.3.3 即这三处谁选执行就以谁的结果为准
			            thenCalledOrThrow = true
			            return reject(r)
			          })
			        } else { // 2.3.3.4
			          resolve(x)
			        }
			      } catch (e) { // 2.3.3.2
			        if (thenCalledOrThrow) return // 2.3.3.3.3 即这三处谁选执行就以谁的结果为准
			        thenCalledOrThrow = true
			        return reject(e)
			      }
			    } else { // 2.3.4
			      resolve(x)
			    }
			  }
			  ```
	- 原则上，`promise.then(onResolved, onRejected)`里的这两项函数需要[[#green]]==异步调用==
	- > In practice, this requirement ensures that onFulfilled and onRejected execute asynchronously, after the event loop turn in which then is called, and with a fresh stack.
	- 所以我们需要对我们的代码做一点变动，即在四个地方加上setTimeout(fn, 0)。
	- 至此，我们就实现了一个的Promise，完整代码如下：
	- ```
	  try {
	    module.exports = Promise
	  } catch (e) {}
	  
	  function Promise(executor) {
	    var self = this
	  
	    self.status = 'pending'
	    self.onResolvedCallback = []
	    self.onRejectedCallback = []
	  
	    function resolve(value) {
	      if (value instanceof Promise) {
	        return value.then(resolve, reject)
	      }
	      setTimeout(function() { // 异步执行所有的回调函数
	        if (self.status === 'pending') {
	          self.status = 'resolved'
	          self.data = value
	          for (var i = 0; i < self.onResolvedCallback.length; i++) {
	            self.onResolvedCallback[i](value)
	          }
	        }
	      })
	    }
	  
	    function reject(reason) {
	      setTimeout(function() { // 异步执行所有的回调函数
	        if (self.status === 'pending') {
	          self.status = 'rejected'
	          self.data = reason
	          for (var i = 0; i < self.onRejectedCallback.length; i++) {
	            self.onRejectedCallback[i](reason)
	          }
	        }
	      })
	    }
	  
	    try {
	      executor(resolve, reject)
	    } catch (reason) {
	      reject(reason)
	    }
	  }
	  
	  function resolvePromise(promise2, x, resolve, reject) {
	    var then
	    var thenCalledOrThrow = false
	  
	    if (promise2 === x) {
	      return reject(new TypeError('Chaining cycle detected for promise!'))
	    }
	  
	    if (x instanceof Promise) {
	      if (x.status === 'pending') { //because x could resolved by a Promise Object
	        x.then(function(v) {
	          resolvePromise(promise2, v, resolve, reject)
	        }, reject)
	      } else { //but if it is resolved, it will never resolved by a Promise Object but a static value;
	        x.then(resolve, reject)
	      }
	      return
	    }
	  
	    if ((x !== null) && ((typeof x === 'object') || (typeof x === 'function'))) {
	      try {
	        then = x.then //because x.then could be a getter
	        if (typeof then === 'function') {
	          then.call(x, function rs(y) {
	            if (thenCalledOrThrow) return
	            thenCalledOrThrow = true
	            return resolvePromise(promise2, y, resolve, reject)
	          }, function rj(r) {
	            if (thenCalledOrThrow) return
	            thenCalledOrThrow = true
	            return reject(r)
	          })
	        } else {
	          resolve(x)
	        }
	      } catch (e) {
	        if (thenCalledOrThrow) return
	        thenCalledOrThrow = true
	        return reject(e)
	      }
	    } else {
	      resolve(x)
	    }
	  }
	  
	  Promise.prototype.then = function(onResolved, onRejected) {
	    var self = this
	    var promise2
	    onResolved = typeof onResolved === 'function' ? onResolved : function(v) {
	      return v
	    }
	    onRejected = typeof onRejected === 'function' ? onRejected : function(r) {
	      throw r
	    }
	  
	    if (self.status === 'resolved') {
	      return promise2 = new Promise(function(resolve, reject) {
	        setTimeout(function() { // 异步执行onResolved
	          try {
	            var x = onResolved(self.data)
	            resolvePromise(promise2, x, resolve, reject)
	          } catch (reason) {
	            reject(reason)
	          }
	        })
	      })
	    }
	  
	    if (self.status === 'rejected') {
	      return promise2 = new Promise(function(resolve, reject) {
	        setTimeout(function() { // 异步执行onRejected
	          try {
	            var x = onRejected(self.data)
	            resolvePromise(promise2, x, resolve, reject)
	          } catch (reason) {
	            reject(reason)
	          }
	        })
	      })
	    }
	  
	    if (self.status === 'pending') {
	      // 这里之所以没有异步执行，是因为这些函数必然会被resolve或reject调用，而resolve或reject函数里的内容已是异步执行，构造函数里的定义
	      return promise2 = new Promise(function(resolve, reject) {
	        self.onResolvedCallback.push(function(value) {
	          try {
	            var x = onResolved(value)
	            resolvePromise(promise2, x, resolve, reject)
	          } catch (r) {
	            reject(r)
	          }
	        })
	  
	        self.onRejectedCallback.push(function(reason) {
	            try {
	              var x = onRejected(reason)
	              resolvePromise(promise2, x, resolve, reject)
	            } catch (r) {
	              reject(r)
	            }
	          })
	      })
	    }
	  }
	  
	  Promise.prototype.catch = function(onRejected) {
	    return this.then(null, onRejected)
	  }
	  
	  Promise.deferred = Promise.defer = function() {
	    var dfd = {}
	    dfd.promise = new Promise(function(resolve, reject) {
	      dfd.resolve = resolve
	      dfd.reject = reject
	    })
	    return dfd
	  }
	  
	  ```
- ## 测试
	- Promise有一个配套的[测试脚本](https://github.com/promises-aplus/promises-tests)，只需要我们在一个CommonJS的模块中暴露一个deferred方法（即exports.deferred方法），就可以了，代码见上述代码的最后。然后执行如下代码即可执行测试：
	- ```
	  npm i -g promises-aplus-tests
	  promises-aplus-tests Promise.js
	  ```
- ## 关于Promise的其它问题
	- ### Promise的性能问题
	  background-color:: pink
		- 理论上说，不能叫做“性能问题”，而只是有可能出现的延迟问题。什么意思呢，记得刚刚我们说需要把4块代码包在setTimeout里吧，先考虑如下代码：
		- ```
		  var start = +new Date()
		  function foo() {
		    setTimeout(function() {
		      console.log('setTimeout')
		      if((+new Date) - start < 1000) {
		        foo()
		      }
		    })
		  }
		  foo()
		  ```
		- 运行上面的代码，会打印出多少次'setTimeout'呢，各位可以自己试一下，不出意外的话，应该是250次左右，我刚刚运行了一次，是241次。[[#green]]==这说明，上述代码中两次setTimeout运行的时间间隔约是4ms（另外，setInterval也是一样的），实事上，这正是浏览器两次Event Loop之间的时间间隔，相关标准各位可以自行查阅。==另外，在Node中，这个时间间隔跟浏览器不一样，经过我的测试，是1ms。
		- 极端情况下，我们有20个Promise链式调用，加上代码运行的时间，那么这个链式调用的第一行代码跟最后一行代码的运行很可能会超过100ms，如果这之间没有对UI有任何更新的话，虽然本质上没有什么性能问题，但可能会造成一定的卡顿或者闪烁，虽然在web应用中这种情形并不常见，但是在Node应用中，确实是有可能出现这样的case的，所以一个能够应用于生产环境的实现有必要把这个延迟消除掉。在Node中，我们可以调用process.nextTick或者setImmediate（[Q就是这么做的](https://link.zhihu.com/?target=https%3A//github.com/kriskowal/q/blob/v1/q.js%23L101)），在浏览器中具体如何做，已经超出了本文的讨论范围，总的来说，就是我们需要实现一个函数，行为跟setTimeout一样，但它需要异步且尽早的调用所有已经加入队列的函数，[这里](http://www.bluejava.com/4NS/Speed-up-your-Websites-with-a-Faster-setTimeout-using-soon)有一个实现。
	- ### 如何停止一个Promise链？
	  background-color:: pink
		- 在一些场景下，我们可能会遇到一个较长的Promise链式调用，在某一步中出现的错误让我们完全没有必要去运行链式调用后面所有的代码，类似下面这样（此处略去了then/catch里的函数）：
		- ```
		  new Promise(function(resolve, reject) {
		    resolve(42)
		  })
		    .then(function(value) {
		      // "Big ERROR!!!"
		    })
		    .catch()
		    .then()
		    .then()
		    .catch()
		    .then()
		  ```
		- 假设这个`Big ERROR!!!`的出现让我们完全没有必要运行后面所有的代码了，但链式调用的后面即有catch，也有then，无论我们是`return`还是`throw`，都不可避免的会进入某一个`catch`或`then`里面，那有没有办法让这个链式调用在`Big ERROR!!!`的后面就停掉，完全不去执行链式调用后面所有回调函数呢？
		- 从一个实现者的角度看问题时，就是[[#green]]==在发生`Big ERROR`后return一个Promise，但这个Promise的executor函数什么也不做==，这就意味着这个Promise将永远处于`pending`状态，由于then返回的Promise会直接取这个永远处于`pending`状态的Promise的状态，于是返回的这个Promise也将一直处于`pending`状态，后面的代码也就一直不会执行了，具体代码如下：
		- ```
		  new Promise(function(resolve, reject) {
		    resolve(42)
		  })
		    .then(function(value) {
		      // "Big ERROR!!!"
		      return new Promise(function(){})
		    })
		    .catch()
		    .then()
		    .then()
		    .catch()
		    .then()
		  ```
		- 这种方式看起来有些山寨，它也确实解决了问题。但它[[#red]]==引入的一个新问题就是链式调用后面的所有回调函数都无法被垃圾回收器回收==（在一个靠谱的实现里，Promise应该在执行完所有回调后删除对所有回调函数的引用以让它们能被回收，在前文的实现里，为了减少复杂度，并没有做这种处理），但[[#green]]==如果我们不使用匿名函数==，而是使用函数定义或者函数变量的话，在需要多次执行的Promise链中，这些函数也都只有一份在内存中，不被回收也是可以接受的。
		- 我们可以将返回一个什么也不做的Promise封装成一个有语义的函数，以增加代码的可读性：
		- ```
		  Promise.cancel = Promise.stop = function() {
		    return new Promise(function(){})
		  }
		  ```
	- ### Promise链上返回的最后一个Promise出错了怎么办？
	  background-color:: pink
		- 考虑如下代码：
		- ```
		  new Promise(function(resolve) {
		    resolve(42)
		  })
		    .then(function(value) {
		      alter(value)
		    })
		  ```
		- 细看最后一行，`alert`被打成了`alter`，那为什么控制台没有报错呢，[[#red]]==因为`alter`所在的函数是被包在`try/catch`块里的，`alter`这个变量找不到就直接抛错了，这个错就正好成了then返回的Promise的rejection reason。==
		-
-