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
	- ### 4.Promise的初始状态为pending，它可以由此状态转换为fulfilled（本文为了一致把此状态叫做resolved）或者rejected，一旦状态确定，就不可以再次转换为其它状态，状态确定的过程称为settle
	-