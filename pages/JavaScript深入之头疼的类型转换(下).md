- > https://github.com/mqyqingfeng/Blog/issues/164
-
- ## 一元操作符 +
	- ```
	  console.log(+'1');
	  ```
	- 当 + 运算符作为一元操作符的时候，查看 [ES5规范1.4.6](http://es5.github.io/#x11.4.6)，会调用 `ToNumber` 处理该值，相当于 `Number('1')`，最终结果返回数字 `1`。
	-