- > https://github.com/mqyqingfeng/Blog/issues/82
-
- ## 块级作用域的出现
	- 通过 var 声明的变量存在变量提升的特性：
	- ```
	  if (condition) {
	      var value = 1;
	  }
	  console.log(value);
	  ```
	- 因为变量提升的原因，代码相当于：
	- ```
	  var value;
	  if (condition) {
	  	value = 1;
	  }
	  console.log(value);
	  ```
	- 除此之外，在 for 循环中：
	- ```
	  for (var i = 0; i < 10; i++) {
	      ...
	  }
	  console.log(i); // 10
	  ```
	- 为了加强对变量生命周期的控制，ECMAScript 6 引入了[[#blue]]==块级作用域==。
	- 块级作用域存在于：
		- 1. 函数内部
		  2. 块中(字符 { 和 } 之间的区域)
- ## let 和 const
	-