- > https://github.com/mqyqingfeng/Blog/issues/159
-
- ## 前言
	- 将值从一种类型转换为另一种类型通常称为类型转换。
	- ES6 前，JavaScript 共有六种数据类型：[[#blue]]==Undefined、Null、Boolean、Number、String、Object==。
- ## 原始值转布尔
	- 使用 **Boolean** 函数将类型转换成布尔类型，在 JavaScript 中，只有 6 种值可以被转换成 false，其他都会被转换成 true。
	- ```
	  console.log(Boolean()) // false
	  
	  console.log(Boolean(false)) // false
	  
	  console.log(Boolean(undefined)) // false
	  console.log(Boolean(null)) // false
	  console.log(Boolean(+0)) // false
	  console.log(Boolean(-0)) // false
	  console.log(Boolean(NaN)) // false
	  console.log(Boolean("")) // false
	  ```
- ## 原始值转数字
	- 使用 **Number** 函数将类型转换成数字类型，如果参数无法被转换为数字，则返回 NaN。
-