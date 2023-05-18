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
	- call 改变了 this 的指向，指向到 foo
	- bar 函数执行了