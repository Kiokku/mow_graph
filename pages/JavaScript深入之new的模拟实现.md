- > https://github.com/mqyqingfeng/Blog/issues/13
-
- ## new
	- 一句话介绍 new:
	- > new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一
- ## 初步实现
	- 分析：
	- 因为 new 的结果是一个新对象，所以在模拟实现的时候，我们也要建立一个新对象，假设这个对象叫 obj，因为 obj 会具有 Otaku 构造函数里的属性，想想经典继承的例子，我们可以使用 Otaku.apply(obj, arguments)来给 obj 添加新的属性。
	- 在 JavaScript 深入系列第一篇中，我们便讲了原型与原型链，我们知道实例的 __proto__ 属性会指向构造函数的 prototype，也正是因为建立起这样的关系，实例可以访问原型上的属性。
	- 现在，我们可以尝试着写第一版了：
	- ```
	  // 第一版代码
	  function objectFactory() {
	  
	      var obj = new Object(),
	  
	      Constructor = [].shift.call(arguments);
	  
	      obj.__proto__ = Constructor.prototype;
	  
	      Constructor.apply(obj, arguments);
	  
	      return obj;
	  
	  };
	  ```
	-