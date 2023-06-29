- > https://github.com/mqyqingfeng/Blog/issues/107
-
- ## 前言
	- 我们或多或少都听过“数据绑定”这个词，“数据绑定”的关键在于监听数据的变化，可是对于这样一个对象：`var obj = {value: 1}`，我们该怎么知道 obj 发生了改变呢？
- ## defineProperty
	- ES5 提供了 `Object.defineProperty` 方法，该方法可以在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。
	- **语法**:`Object.defineProperty(obj, prop, descriptor)`
	- **参数**
		- `obj`: 要在其上定义属性的对象。
		- `prop`:  要定义或修改的属性的名称。
		- `descriptor`: 将被定义或修改的属性的描述符。
	- 举个例子：
	- ```
	  var obj = {};
	  Object.defineProperty(obj, "num", {
	      value : 1,
	      writable : true,
	      enumerable : true,
	      configurable : true
	  });
	  //  对象 obj 拥有属性 num，值为 1
	  ```
	- 函数的第三个参数 descriptor 所表示的属性描述符有两种形式：**数据描述符和存取描述符**。
	- ### 两者均具有以下两种键值：
	  background-color:: pink
		- **configurable**：当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，也能够被删除。默认为 false。
		- **enumerable**：当且仅当该属性的 enumerable 为 true 时，该属性才能够出现在对象的枚举属性中。默认为 false。
	- ### 数据描述符同时具有以下可选键值：
	  background-color:: pink
		- **value**：该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认为 undefined。
		- **writable**：当且仅当该属性的 writable 为 true 时，该属性才能被[[#green]]==赋值运算符==改变。默认为 false。
	- ### 存取描述符同时具有以下可选键值：
	  background-color:: pink
		-