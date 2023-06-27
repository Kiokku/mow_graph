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
		-