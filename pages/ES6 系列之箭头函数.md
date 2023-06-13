- > https://github.com/mqyqingfeng/Blog/issues/85
-
- ## 比较
	- 箭头函数与普通函数的主要区别包括：
	- ### 1.没有 this
	  background-color:: blue
		- **箭头函数没有 this，所以需要通过查找作用域链来确定 this 的值。**
		- 这就意味着如果箭头函数被非箭头函数包含，this 绑定的就是最近一层非箭头函数的 this。
		-