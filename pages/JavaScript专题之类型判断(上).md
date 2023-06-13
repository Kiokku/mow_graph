- > https://github.com/mqyqingfeng/Blog/issues/28
-
- ## typeof
	- 引用《JavaScript权威指南》中对 typeof 的介绍：
	- > typeof 是一元操作符，放在其单个操作数的前面，操作数可以是任意类型。返回值为表示操作数类型的一个字符串。
	- 在 ES6 前，JavaScript 共**六种数据类型**，分别是：
	- Undefined、Null、Boolean、Number、String、Object
	- 当我们使用 typeof 对这些数据类型的值进行操作的时候，返回的结果却不是一一对应，分别是：
	- undefined、object、boolean、number、string、object
	- [[#blue]]==Null 和 Object 类型都返回了 object 字符串。==
	- 尽管不能一一对应，但是 typeof 却能检测出函数类型：
	- ```
	  function a() {}
	  
	  console.log(typeof a); // function
	  ```
	- 所以 typeof 能检测出六种类型的值:
		- `undefined`
		  id:: 64883711-86e8-4b65-b7a8-74ace81d23f0
		- `object`
		- `boolean`
		- `number`
		- `string`
		- `function`
	- 除此之外 Object 下还有很多细分的类型，如 [[#red]]==Array、Date、RegExp、Error== 等。
- ## Object.prototype.toString
	- > When the toString method is called, the following steps are taken:
	  >
	  >> 1. If the **this** value is **undefined**, return "**[object Undefined]**".
	  >> 2. If the **this** value is **null**, return "**[object Null]**".
	  >> 3. Let *O* be the result of calling ToObject passing the **this** value as the argument.
	  >> 4. Let *class* be the value of the [[Class]] internal property of *O*.
	  >> 5. Return the String value that is the result of concatenating the three Strings "**[object** ", *class*, and "**]**".
-