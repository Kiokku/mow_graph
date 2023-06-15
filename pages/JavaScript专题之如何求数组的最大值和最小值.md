- > https://github.com/mqyqingfeng/Blog/issues/35
-
- ## Math.max
	- JavaScript 提供了 Math.max 函数返回一组数中的最大值，用法是：`Math.max([value1[,value2, ...]])`
	- 值得注意的是：
		- 1. 如果有[[#red]]==任一参数不能被转换为数值==，则结果为 NaN。
		  2. max 是 Math 的静态方法，所以应该像这样使用：Math.max()，而不是作为 Math 实例的方法 ([[#red]]==简单的来说，就是不使用 new== )
		  3. 如果没有参数，则结果为 `-Infinity` (注意是负无穷大)
- ## 原始方法
	- 最原始的方法，莫过于循环遍历一遍：
	- ```
	  var arr = [6, 4, 1, 8, 2, 11, 23];
	  
	  var result = arr[0];
	  for (var i = 1; i < arr.length; i++) {
	      result =  Math.max(result, arr[i]);
	  }
	  console.log(result);
	  ```
- ## reduce
	- ```
	  var arr = [6, 4, 1, 8, 2, 11, 23];
	  
	  var result = arr.reduce((prev, next) => Math.max(prev, next));
	  console.log(result);
	  ```
- ## 排序
	- ```
	  var arr = [6, 4, 1, 8, 2, 11, 23];
	  
	  arr.sort(function(a,b){return a - b;});
	  console.log(arr[arr.length - 1])
	  ```
- ## eval
	- Math.max 支持[[#blue]]==传多个参数==来进行比较，那么我们如何将一个数组转换成参数传进 Math.max 函数呢？eval 便是一种
	- ```
	  var arr = [6, 4, 1, 8, 2, 11, 23];
	  
	  var max = eval("Math.max(" + arr + ")");
	  console.log(max)
	  ```
- ## apply
	- ```
	  var arr = [6, 4, 1, 8, 2, 11, 23];
	  console.log(Math.max.apply(null, arr))
	  ```
- ## ES6 扩展运算符
	- ```
	  var arr = [6, 4, 1, 8, 2, 11, 23];
	  console.log(Math.max(...arr))
	  ```
	-
-