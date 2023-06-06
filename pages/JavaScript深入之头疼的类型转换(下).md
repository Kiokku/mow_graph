- > https://github.com/mqyqingfeng/Blog/issues/164
-
- ## 一元操作符 +
	- ```
	  console.log(+'1');
	  ```
	- 当 + 运算符作为一元操作符的时候，查看 [ES5规范1.4.6](http://es5.github.io/#x11.4.6)，会调用 `ToNumber` 处理该值，相当于 `Number('1')`，最终结果返回数字 `1`。
	- ```
	  console.log(+[]); // 0
	  console.log(+['1']); // 1
	  console.log(+['1', '2', '3']); // NaN
	  console.log(+{}); // NaN
	  ```
- ## 二元操作符 +
	- ### 规范
	  background-color:: blue
		- 规范地址：[http://es5.github.io/#x11.6.1](http://es5.github.io/#x11.6.1)
		- 当计算 value1 + value2时：
			- id:: 647ee3cc-cabc-4e12-8176-c1180a5ff3f7
			  1. lprim = ToPrimitive(value1)
			  2. rprim = ToPrimitive(value2)
			  3. 如果 lprim 是字符串或者 rprim 是字符串，那么返回 ToString(lprim) 和 ToString(rprim)的拼接结果
			  4. 返回 ToNumber(lprim) 和 ToNumber(rprim)的运算结果
	- ### 1.Null 与数字
	  background-color:: green
		- ```
		  console.log(null + 1); // 1
		  ```
		- 按照规范的步骤进行分析：
			- 1. lprim = ToPrimitive(null) 因为null是基本类型，直接返回，所以 lprim = null
			  2. rprim = ToPrimitive(1) 因为 1 是基本类型，直接返回，所以 rprim = null
			  3. lprim 和 rprim 都不是字符串
			  4. 返回 ToNumber(null) 和 ToNumber(1) 的运算结果
	- ### 2.数组与数组
	  background-color:: green
		- ```
		  console.log([] + []); // ""
		  ```
		- 1. lprim = ToPrimitive([])，[]是数组，相当于ToPrimitive([], Number)，先调用valueOf方法，返回对象本身，因为不是原始值，调用toString方法，返回空字符串""
		  2. rprim类似。
		  3. lprim和rprim都是字符串，执行拼接操作
	- ### 3.数组与对象
	  background-color:: green
		- ```
		  // 两者结果一致
		  console.log([] + {}); // 
		  console.log({} + []);
		  ```
		- - lprim = ToPrimitive([])，lprim = ""
		  - rprim = ToPrimitive({})，相当于调用 ToPrimitive({}, Number)，先调用 valueOf 方法，返回对象本身，因为不是原始值，调用 toString 方法，返回 "[object Object]"
		  - lprim 和 rprim 都是字符串，执行拼接操作