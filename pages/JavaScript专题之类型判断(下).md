- > https://github.com/mqyqingfeng/Blog/issues/30
-
- ## plainObject
  id:: 648a87c2-5741-40d9-a704-381260b5c1e3
	- plainObject 来自于 jQuery，可以翻译成纯粹的对象，所谓"纯粹的对象"，就是该对象是通过 "{}" 或 "new Object" 创建的，该对象含有零个或者多个键值对。
	- 之所以要判断是不是 plainObject，是为了跟其他的 JavaScript对象如 null，数组，宿主对象（documents）等作区分，因为这些用 typeof 都会返回object。
	- ```
	  function Person(name) {
	      this.name = name;
	  }
	  
	  console.log($.isPlainObject({})) // true
	  
	  console.log($.isPlainObject(new Object)) // true
	  
	  console.log($.isPlainObject(Object.create(null))); // true
	  
	  console.log($.isPlainObject(Object.assign({a: 1}, {b: 2}))); // true
	  
	  console.log($.isPlainObject(new Person('yayu'))); // false
	  
	  console.log($.isPlainObject(Object.create({}))); // false
	  ```
	- 由此我们可以看到，除了 {} 和 new Object 创建的之外，jQuery [[#blue]]==认为一个没有原型的对象也是一个纯粹的对象。==
	- 实际上随着 jQuery 版本的提升，isPlainObject 的实现也在变化，我们今天讲的是 3.0 版本下的 isPlainObject，我们直接看源码:
	- ```
	  // 上节中写 type 函数时，用来存放 toString 映射结果的对象
	  var class2type = {};
	  
	  // 相当于 Object.prototype.toString
	  var toString = class2type.toString;
	  
	  // 相当于 Object.prototype.hasOwnProperty
	  var hasOwn = class2type.hasOwnProperty;
	  
	  function isPlainObject(obj) {
	      var proto, Ctor;
	  
	      // 排除掉明显不是obj的以及一些宿主对象如Window
	      if (!obj || toString.call(obj) !== "[object Object]") {
	          return false;
	      }
	  
	      /**
	       * getPrototypeOf es5 方法，获取 obj 的原型
	       * 以 new Object 创建的对象为例的话
	       * obj.__proto__ === Object.prototype
	       */
	      proto = Object.getPrototypeOf(obj);
	  
	      // 没有原型的对象是纯粹的，Object.create(null) 就在这里返回 true
	      if (!proto) {
	          return true;
	      }
	  
	      /**
	       * 以下判断通过 new Object 方式创建的对象
	       * 判断 proto 是否有 constructor 属性，如果有就让 Ctor 的值为 proto.constructor
	       * 如果是 Object 函数创建的对象，Ctor 在这里就等于 Object 构造函数
	       */
	      Ctor = hasOwn.call(proto, "constructor") && proto.constructor;
	  
	      // 在这里判断 Ctor 构造函数是不是 Object 构造函数，用于区分自定义构造函数和 Object 构造函数
	      return typeof Ctor === "function" && hasOwn.toString.call(Ctor) === hasOwn.toString.call(Object);
	  }
	  ```
- ## EmptyObject
	- jQuery提供了 isEmptyObject 方法来判断是否是空对象，代码简单，我们直接看源码：
	- ```
	  function isEmptyObject( obj ) {
	  
	          var name;
	  
	          for ( name in obj ) {
	              return false;
	          }
	  
	          return true;
	  }
	  ```
	- 根据这个源码我们可以看出isEmptyObject实际上判断的并不仅仅是空对象。
	- ```
	  console.log(isEmptyObject({})); // true
	  console.log(isEmptyObject([])); // true
	  console.log(isEmptyObject(null)); // true
	  console.log(isEmptyObject(undefined)); // true
	  console.log(isEmptyObject(1)); // true
	  console.log(isEmptyObject('')); // true
	  console.log(isEmptyObject(true)); // true
	  ```
	- 但是既然 jQuery 是这样写，可能是因为考虑到实际开发中 isEmptyObject 用来判断 {} 和 {a: 1} 是足够的吧。如果真的是只判断 {}，完全可以结合上篇写的 type 函数筛选掉不适合的情况。
- ## Window对象
	- Window 对象作为客户端 JavaScript 的全局对象，它有一个 window 属性指向自身
	- ```
	  function isWindow( obj ) {
	      return obj != null && obj === obj.window;
	  }
	  ```
- ## isArrayLike
	- isArrayLike，看名字可能会让我们觉得这是判断类数组对象的，其实不仅仅是这样，jQuery 实现的 isArrayLike，[[#blue]]==数组和类数组都会返回 true==。
	- ```
	  function isArrayLike(obj) {
	  
	      // obj 必须有 length属性
	      var length = !!obj && "length" in obj && obj.length;
	      var typeRes = type(obj);
	  
	      // 排除掉函数和 Window 对象
	      if (typeRes === "function" || isWindow(obj)) {
	          return false;
	      }
	  
	      return typeRes === "array" || length === 0 ||
	          typeof length === "number" && length > 0 && (length - 1) in obj;
	  }
	  ```
	- 如果 isArrayLike 返回true，至少要满足三个条件之一：
		- 1. 是数组
		  2. 长度为 0
		  3. length 属性是大于 0 的数字类型，并且obj[length - 1]必须存在
	- 为什么长度为 0 就可以直接判断为 true 呢？
	- ```
	  function a(){
	      console.log(isArrayLike(arguments))
	  }
	  a();
	  ```
	- 如果我们去掉length === 0 这个判断，就会打印 false，然而我们都知道 arguments 是一个类数组对象，这里是应该返回 true 的。
	- 第三个条件：length 是数字，并且 length > 0 且最后一个元素存在。
	- 当我们在数组中用逗号直接跳过的时候，我们认为该元素是不存在的，类数组对象中也就不用写这个元素，但是最后一个元素是一定要写的，要不然 length 的长度就不会是最后一个元素的 key 值加 1。比如数组可以这样写
	- ```
	  var arr = [1,,];
	  console.log(arr.length) // 2
	  ```
	- 但是类数组对象就只能写成：
	- ```
	  var arrLike = {
	      0: 1,
	      length: 1
	  }
	  ```
	- 除了 jQuery 之外，很多库都有对 isArrayLike 的实现，比如 underscore:
	- ```
	  var MAX_ARRAY_INDEX = Math.pow(2, 53) - 1;
	  
	  var isArrayLike = function(collection) {
	      var length = getLength(collection);
	      return typeof length == 'number' && length >= 0 && length <= MAX_ARRAY_INDEX;
	  };
	  ```
- ## isElement
	- isElement 判断是不是 DOM 元素。
	- ```
	  isElement = function(obj) {
	      return !!(obj && obj.nodeType === 1);
	  };
	  ```
	-