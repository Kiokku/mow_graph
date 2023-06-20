- > https://github.com/mqyqingfeng/Blog/issues/41
-
- ## 相等
	- `underscore`中的`eq`函数：
		- 1. NaN 和 NaN 是相等
		  2. [1] 和 [1] 是相等
		  3. {value: 1} 和 {value: 1} 是相等
		  4. 1 和 new Number(1) 是相等
		  5. 'Curly' 和 new String('Curly') 是相等
		  6. true 和 new Boolean(true) 是相等
- ## 目标
	- 我们的目标是写一个 eq 函数用来判断两个参数是否相等，使用效果如下：
	- ```
	  function eq(a, b) { ... }
	  
	  var a = [1];
	  var b = [1];
	  console.log(eq(a, b)) // true
	  ```
- ## +0 与 -0
	-