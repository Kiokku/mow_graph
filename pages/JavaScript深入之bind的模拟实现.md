- > [https://github.com/mqyqingfeng/Blog/issues/12](https://github.com/mqyqingfeng/Blog/issues/12)
-
- ## bind
	- 一句话介绍 bind:
	- > bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。(来自于 MDN )
	- 由此我们可以首先得出 bind 函数的两个特点：
		- 返回一个函数
		- 可以传入参数
- ## 返回函数的模拟实现
	- 从第一个特点开始，我们举个例子：
		- ```
		  var foo = {
		      value: 1
		  };
		  
		  function bar() {
		      console.log(this.value);
		  }
		  
		  // 返回了一个函数
		  var bindFoo = bar.bind(foo); 
		  
		  bindFoo(); // 1
		  ```
	- 关于指定 this 的指向，我们可以使用 call 或者 apply 实现
	- ```
	  // 第一版
	  Function.prototype.bind2 = function (context) {
	      var self = this;
	      return function () {
	          return self.apply(context);
	      }
	  
	  }
	  ```
	- ```
	  var foo = {
	      value: 1
	  };
	  
	  function bar() {
	  	return this.value;
	  }
	  
	  var bindFoo = bar.bind(foo);
	  
	  console.log(bindFoo()); // 1
	  ```
- ## 传参的模拟实现
	- 接下来看第二点，可以传入参数。这个就有点让人费解了，我在 bind 的时候，是否可以传参呢？我在执行 bind 返回的函数的时候，可不可以传参呢？让我们看个例子：
		- ```
		  var foo = {
		      value: 1
		  };
		  
		  function bar(name, age) {
		      console.log(this.value);
		      console.log(name);
		      console.log(age);
		  
		  }
		  
		  var bindFoo = bar.bind(foo, 'daisy');
		  bindFoo('18');
		  // 1
		  // daisy
		  // 18
		  ```
	- 函数需要传 name 和 age 两个参数，竟然还可以在 bind 的时候，只传一个 name，在执行返回的函数的时候，再传另一个参数 age!
	- 我们用 arguments 进行处理：
	- ```
	  // 第二版
	  Function.prototype.bind2 = function (context) {
	  
	      var self = this;
	      // 获取bind2函数从第二个参数到最后一个参数
	      var args = Array.prototype.slice.call(arguments, 1);
	  
	      return function () {
	          // 这个时候的arguments是指bind返回的函数传入的参数
	          var bindArgs = Array.prototype.slice.call(arguments);
	          return self.apply(context, args.concat(bindArgs));
	      }
	  
	  }
	  ```
- ## 构造函数效果的模拟实现
	- 完成了这两点，最难的部分到啦！因为 bind 还有一个特点，就是
	- > 一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。