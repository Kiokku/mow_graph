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
	  >
	  >> 2. If the **this** value is **null**, return "**[object Null]**".
	  >
	  >> 3. Let *O* be the result of calling ToObject passing the **this** value as the argument.
	  >
	  >> 4. Let *class* be the value of the [[Class]] internal property of *O*.
	  >
	  >> 5. Return the String value that is the result of concatenating the three Strings "**[object** ", *class*, and "**]**".
	- 当 toString 方法被调用的时候，下面的步骤会被执行：
		- 1. 如果 this 值是 undefined，就返回 [object Undefined]
		  2. 如果 this 的值是 null，就返回 [object Null]
		  3. 让 O 成为 ToObject(this) 的结果
		  4. 让 class 成为 O 的内部属性 [[Class]] 的值
		  5. 最后返回由 "[object " 和 class 和 "]" 三个部分组成的字符串
	- ```
	  console.log(Object.prototype.toString.call(undefined)) // [object Undefined]
	  console.log(Object.prototype.toString.call(null)) // [object Null]
	  
	  var date = new Date();
	  console.log(Object.prototype.toString.call(date)) // [object Date]
	  ```
	- [[#blue]]==可以用 Object.prototype.toString 方法识别出至少 14种类型！==
	- ```
	  // 以下是11种：
	  var number = 1;          // [object Number]
	  var string = '123';      // [object String]
	  var boolean = true;      // [object Boolean]
	  var und = undefined;     // [object Undefined]
	  var nul = null;          // [object Null]
	  var obj = {a: 1}         // [object Object]
	  var array = [1, 2, 3];   // [object Array]
	  var date = new Date();   // [object Date]
	  var error = new Error(); // [object Error]
	  var reg = /a/g;          // [object RegExp]
	  var func = function a(){}; // [object Function]
	  
	  function checkType() {
	      for (var i = 0; i < arguments.length; i++) {
	          console.log(Object.prototype.toString.call(arguments[i]))
	      }
	  }
	  
	  checkType(number, string, boolean, und, nul, obj, array, date, error, reg, func)
	  ```
	- 除了以上 11 种之外，还有：
	- ```
	  console.log(Object.prototype.toString.call(Math)); // [object Math]
	  console.log(Object.prototype.toString.call(JSON)); // [object JSON]
	  ```
	- 除了以上 13 种之外，还有：
	- ```
	  function a() {
	    console.log(Object.prototype.toString.call(arguments)); // [object Arguments]
	  }
	  a();
	  ```
- ## type API
	- 写一个 type 函数能**检测各种类型的值**，如果是基本类型，就使用 typeof，引用类型就使用 toString。此外鉴于 typeof 的结果是小写，我也希望所有的结果都是小写。
	- 考虑到实际情况下并不会检测 Math 和 JSON，所以去掉这两个类型的检测。
	- ```
	  // 第一版
	  var class2type = {};
	  
	  // 生成class2type映射
	  "Boolean Number String Function Array Date RegExp Object Error Null Undefined".split(" ").map(function(item, index) {
	      class2type["[object " + item + "]"] = item.toLowerCase();
	  })
	  
	  function type(obj) {
	      return typeof obj === "object" || typeof obj === "function" ?
	          class2type[Object.prototype.toString.call(obj)] || "object" :
	          typeof obj;
	  }
	  ```
	- [[#red]]==兼容性问题???==：在 IE6 中，null 和 undefined 会被 Object.prototype.toString 识别成 [object Object]。
	- ```
	  // 第二版
	  var class2type = {};
	  
	  // 生成class2type映射
	  "Boolean Number String Function Array Date RegExp Object Error".split(" ").map(function(item, index) {
	      class2type["[object " + item + "]"] = item.toLowerCase();
	  })
	  
	  function type(obj) {
	      // 一箭双雕
	      if (obj == null) {
	          return obj + "";
	      }
	      return typeof obj === "object" || typeof obj === "function" ?
	          class2type[Object.prototype.toString.call(obj)] || "object" :
	          typeof obj;
	  }
	  ```