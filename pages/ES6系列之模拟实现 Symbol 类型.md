- > https://github.com/mqyqingfeng/Blog/issues/87
-
- ## 回顾
	- ES6 引入了一种新的原始数据类型 Symbol，表示独一无二的值。
	- **1. Symbol 值通过 Symbol 函数生成，使用 typeof，结果为 "symbol"**
		- ```
		  var s = Symbol();
		  console.log(typeof s); // "symbol"
		  ```
		-