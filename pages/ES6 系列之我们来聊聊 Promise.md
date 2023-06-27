- > https://github.com/mqyqingfeng/Blog/issues/98
-
- ## 回调
	- 说起 Promise，我们一般都会从回调或者回调地狱说起，那么使用回调到底会导致哪些不好的地方呢？
	- ### 1. 回调嵌套
	  background-color:: pink
		- ```
		  doA( function(){
		      doB();
		  
		      doC( function(){
		          doD();
		      } )
		  
		      doE();
		  } );
		  
		  doF();
		  ```
		- 执行的顺序为：
		- `doA() -> doF() -> doB() -> doC() -> doE() -> doD()`
	- ### 2. 控制反转
	  background-color:: pink
		- 正常书写代码的时候，我们理所当然可以控制自己的代码，然而当我们使用回调的时候，这个回调函数是否能接着执行，其实取决于使用回调的那个 API.
		- 为了避免出现这样的问题，你可以在自己的回调函数中加入判断，可是万一又因为某个错误这个回调函数没有执行呢？
		- 我们总结一下这些情况：
			- 回调函数执行多次
			- 回调函数没有执行
			- 回调函数有时同步执行有时异步执行
- ## 回调地狱
	- ### 1. 难以复用
	  background-color:: pink
		- 回调的顺序确定下来之后，想对其中的某些环节进行复用也很困难，牵一发而动全身。
	- ### 2. 堆栈信息被断开
	  background-color:: pink
		- 如果 A 函数中调用了 B 函数，JavaScript 会先将 A 函数的执行上下文压入栈中，再将 B 函数的执行上下文压入栈中，当 B 函数执行完毕，将 B 函数执行上下文出栈，当 A 函数执行完毕后，将 A 函数执行上下文出栈。
		- 这样的好处在于，[[#green]]==我们如果中断代码执行，可以检索完整的堆栈信息，从中获取任何我们想获取的信息。==
		- 可是异步回调函数并非如此，其实是将回调函数加入**任务队列**中，代码继续执行，直至主线程完成后，才会从任务队列中选择已经完成的任务，并将其加入栈中，此时栈中只有这一个执行上下文，如果回调报错，也无法获取调用该异步操作时的栈中的信息，不容易判定哪里出现了错误。
	- ### 3. 借助外层变量
	  background-color:: pink
- ## Promise
	- ### 1. 嵌套问题
	  background-color:: green
		- 举个例子：
		- ```
		  request(url, function(err, res, body) {
		      if (err) handleError(err);
		      fs.writeFile('1.txt', body, function(err) {
		          request(url2, function(err, res, body) {
		              if (err) handleError(err)
		          })
		      })
		  });
		  ```
		- 使用 Promise 后：
		- ```
		  request(url)
		  .then(function(result) {
		      return writeFileAsynv('1.txt', result)
		  })
		  .then(function(result) {
		      return request(url2)
		  })
		  .catch(function(e){
		      handleError(e)
		  });
		  ```
	- ### 2. 控制反转再反转
	  background-color:: green
		- 前面我们讲到使用第三方回调 API 的时候，可能会遇到如下问题：
			- 1. 回调函数执行多次
			  2 回调函数没有执行
			  3. 回调函数有时同步执行有时异步执行
		- 对于**第一个问题**，Promise 只能 resolve 一次，剩下的调用都会被忽略。
		- 对于**第二个问题**，我们可以使用 Promise.race 函数来解决：
			- ```
			  function timeoutPromise(delay) {
			      return new Promise( function(resolve,reject){
			          setTimeout( function(){
			              reject( "Timeout!" );
			          }, delay );
			      } );
			  }
			  
			  Promise.race( [
			      foo(),
			      timeoutPromise( 3000 )
			  ] )
			  .then(function(){}, function(err){});
			  ```
		- 对于**第三个问题**，为什么有的时候会同步执行有的时候回异步执行呢？
			- ```
			  var cache = {...};
			  function downloadFile(url) {
			        if(cache.has(url)) {
			              // 如果存在cache，这里为同步调用
			             return Promise.resolve(cache.get(url));
			        }
			       return fetch(url).then(file => cache.set(url, file)); // 这里为异步调用
			  }
			  console.log('1');
			  getValue.then(() => console.log('2'));
			  console.log('3');
			  ```
			- 如果将这种同步和异步混用的代码作为内部实现，只暴露接口给外部调用，调用方由于无法判断是到底是异步还是同步状态，影响程序的可维护性和可测试性。
			- [[#red]]==简单来说就是同步和异步共存的情况无法保证程序逻辑的一致性。==
			- 然而 Promise 解决了这个问题，我们来看个例子：
			- ```
			  var promise = new Promise(function (resolve){
			      resolve();
			      console.log(1);
			  });
			  promise.then(function(){
			      console.log(2);
			  });
			  console.log(3);
			  
			  // 1 3 2
			  ```
			- > [[#blue]]==PromiseA+ 规范也有明确的规定：==
			  > 实践中要确保 onFulfilled 和 onRejected 方法异步执行，且应该在 then 方法被调用的那一轮事件循环之后的新执行栈中执行。
- ## Promise 反模式
	- ### 1. Promise 嵌套
	  background-color:: green
		- ```
		  // bad
		  loadSomething().then(function(something) {
		      loadAnotherthing().then(function(another) {
		          DoSomethingOnThem(something, another);
		      });
		  });
		  
		  // good
		  Promise.all([loadSomething(), loadAnotherthing()])
		  .then(function ([something, another]) {
		      DoSomethingOnThem(...[something, another]);
		  });
		  ```
	- ### 2. 断开的 Promise 链
	  background-color:: green
		- ```
		  // bad
		  function anAsyncCall() {
		      var promise = doSomethingAsync();
		      promise.then(function() {
		          somethingComplicated();
		      });
		  
		      return promise;
		  }
		  
		  // good
		  function anAsyncCall() {
		      var promise = doSomethingAsync();
		      return promise.then(function() {
		          somethingComplicated()
		      });
		  }
		  ```
	- ### 3. 混乱的集合
	  background-color:: green
		- ```
		  // bad
		  function workMyCollection(arr) {
		      var resultArr = [];
		      function _recursive(idx) {
		          if (idx >= resultArr.length) return resultArr;
		  
		          return doSomethingAsync(arr[idx]).then(function(res) {
		              resultArr.push(res);
		              return _recursive(idx + 1);
		          });
		      }
		  
		      return _recursive(0);
		  }
		  
		  //good
		  function workMyCollection(arr) {
		      return Promise.all(arr.map(function(item) {
		          return doSomethingAsync(item);
		      }));
		  }
		  
		  //good
		  function workMyCollection(arr) {
		      return arr.reduce(function(promise, item) {
		          return promise.then(function(result) {
		              return doSomethingAsyncWithResult(item, result);
		          });
		      }, Promise.resolve());
		  }
		  ```
	- ### 4. catch
	  background-color:: green
		- ```
		  // bad
		  somethingAync.then(function() {
		      return somethingElseAsync();
		  }, function(err) {
		      handleMyError(err);
		  });
		  ```
		- 如果 somethingElseAsync 抛出错误，是无法被捕获的。你可以写成：
		- ```
		  // good
		  somethingAsync
		  .then(function() {
		      return somethingElseAsync()
		  })
		  .then(null, function(err) {
		      handleMyError(err);
		  });
		  
		  // good
		  somethingAsync()
		  .then(function() {
		      return somethingElseAsync();
		  })
		  .catch(function(err) {
		      handleMyError(err);
		  });
		  ```
- ## 红绿灯问题
	- > 🏁 **题目**：红灯三秒亮一次，绿灯一秒亮一次，黄灯2秒亮一次；如何让三个灯不断交替重复亮灯？（用 Promse 实现）
	- 三个亮灯函数已经存在，利用 then 和递归实现：
	- ```
	  function red(){
	      console.log('red');
	  }
	  function green(){
	      console.log('green');
	  }
	  function yellow(){
	      console.log('yellow');
	  }
	  
	  var light = function(timmer, cb){
	      return new Promise(function(resolve, reject) {
	          setTimeout(function() {
	              cb();
	              resolve();
	          }, timmer);
	      });
	  };
	  
	  var step = function() {
	      Promise.resolve().then(function(){
	          return light(3000, red);
	      }).then(function(){
	          return light(2000, green);
	      }).then(function(){
	          return light(1000, yellow);
	      }).then(function(){
	          step();
	      });
	  }
	  
	  step();
	  ```
- ## promisify
	- 因为 callback 语法传参比较明确，最后一个参数传入回调函数，回调函数的第一个参数是一个错误信息，如果没有错误，就是 null，所以我们可以直接写出一个简单的 promisify 方法：
	- ```
	  function promisify(original) {
	      return function (...args) {
	          return new Promise((resolve, reject) => {
	              args.push(function callback(err, ...values) {
	                  if (err) {
	                      return reject(err);
	                  }
	                  return resolve(...values)
	              });
	              original.call(this, ...args);
	          });
	      };
	  }
	  ```
- ## Promise 的局限性
	- ### 1. 错误被吃掉
	  background-color:: pink
		- 举个例子：
		- ```
		  throw new Error('error');
		  console.log(233333);
		  ```
		- > 在这种情况下，因为 throw error 的缘故，代码被阻断执行，并不会打印 233333。
		- ```
		  const promise = new Promise(null);
		  console.log(233333);
		  ```
		- > 以上代码依然会被阻断执行，这是因为如果通过无效的方式使用 Promise，并且出现了一个错误阻碍了正常 Promise 的构造，结果会得到一个立刻跑出的异常，而不是一个被拒绝的 Promise。
		- ```
		  let promise = new Promise(() => {
		      throw new Error('error')
		  });
		  console.log(2333333);
		  ```
		- > 这次会正常的打印 `233333`，说明 Promise 内部的错误不会影响到 Promise 外部的代码，而这种情况我们就通常称为 **“吃掉错误”**。
		- 而正是因为错误被吃掉，Promise 链中的错误很容易被忽略掉，这也是为什么会[[#blue]]==一般推荐在 Promise 链的最后添加一个 **catch 函数**，因为对于一个没有错误处理函数的 Promise 链，任何错误都会在链中被传播下去，直到你注册了错误处理函数。==
	- ### 2. 单一值
	  background-color:: pink
		- Promise 只能有一个完成值或一个拒绝原因，然而在真实使用的时候，往往需要传递多个值，一般做法都是构造一个对象或数组，然后再传递，then 中获得这个值后，又会进行取值赋值的操作，每次封装和解封都无疑让代码变得笨重。
	- ### 3. 无法取消
	  background-color:: pink
		- Promise 一旦新建它就会立即执行，无法中途取消。
	- ### 4. 无法得知 pending 状态
	  background-color:: pink
		- 当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。
- ## 参考
	- 1. 《你不知道的 JavaScript 中卷》
	  2. [Promise 的 N 种用法](https://segmentfault.com/l/1500000008757392)
	  3. [JavaScript Promise 迷你书](http://liubin.org/promises-book/#promise-done)
	  4. [Promises/A+规范](http://www.ituring.com.cn/article/66566)
	  5. [Promise 如何使用](https://www.cnblogs.com/ZHONGZHENHUA/p/8486616.html)
	  6. [Promise Anti-patterns](https://github.com/mqyqingfeng/Blog/issues/taoofcode.net/promise-anti-patterns/)
	  7. [一道关于Promise应用的面试题](http://www.cnblogs.com/dojo-lzz/p/5495671.html)