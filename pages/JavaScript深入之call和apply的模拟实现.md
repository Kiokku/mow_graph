- > [https://github.com/mqyqingfeng/Blog/issues/11](https://github.com/mqyqingfeng/Blog/issues/11)
-
- ## call
	- 一句话介绍 call：
	- > call() 方法在使用一个指定的 this 值和若干个指定的参数值的前提下调用某个函数或方法。
	- 举个例子：
	- ```
	  var foo = {
	      value: 1
	  };
	  
	  function bar() {
	      console.log(this.value);
	  }
	  
	  bar.call(foo); // 1
	  ```
	- 注意两点：
		- 1. call 改变了 this 的指向，指向到 foo
		  2. bar 函数执行了
- ## 模拟实现第一步
	- 我们模拟的步骤可以分为：
		- 1. 将函数设为对象的属性
		  2. 执行该函数
		  3. 删除该函数
	- 以上个例子为例，就是：
	- ```
	  // 第一步
	  foo.fn = bar
	  // 第二步
	  foo.fn()
	  // 第三步
	  delete foo.fn
	  ```
	- 根据这个思路，我们可以尝试着去写第一版的 **call2** 函数：
	- ```
	  // 第一版
	  Function.prototype.call2 = function(context) {
	      // 首先要获取调用call的函数，用this可以获取
	      context.fn = this;
	      context.fn();
	      delete context.fn;
	  }
	  
	  // 测试一下
	  var foo = {
	      value: 1
	  };
	  
	  function bar() {
	      console.log(this.value);
	  }
	  
	  bar.call2(foo); // 1
	  ```
- ## 模拟实现第二步
	- call 函数还能给定参数执行函数。举个例子：
	- ```
	  var foo = {
	      value: 1
	  };
	  
	  function bar(name, age) {
	      console.log(name)
	      console.log(age)
	      console.log(this.value);
	  }
	  
	  bar.call(foo, 'kevin', 18);
	  // kevin
	  // 18
	  // 1
	  ```
	-