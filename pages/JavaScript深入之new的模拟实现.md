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
	- 在这一版中，我们：
		- 1. 用new Object() 的方式新建了一个对象 obj
		- 2. 取出第一个参数，就是我们要传入的构造函数。此外因为 shift 会修改原数组，所以 arguments 会被去除第一个参数
		  3. 将 obj 的原型指向构造函数原型，这样 obj 就可以访问到构造函数原型中的属性
		  4. 使用 apply，改变构造函数 this 的指向到新建的对象，这样 obj 就可以访问到构造函数中的属性
		  5. 返回 obj
- ## 返回值效果实现
	- 接下来我们再来看一种情况，假如构造函数有返回值，举个例子：
		- ```
		  function Otaku (name, age) {
		      this.strength = 60;
		      this.age = age;
		  
		      return {
		          name: name,
		          habit: 'Games'
		      }
		  }
		  
		  var person = new Otaku('Kevin', '18');
		  
		  console.log(person.name) // Kevin
		  console.log(person.habit) // Games
		  console.log(person.strength) // undefined
		  console.log(person.age) // undefined
		  ```
	- 在这个例子中，构造函数返回了一个对象，[[#blue]]==在实例 person 中只能访问返回的对象中的属性==。
	- 而且还要注意一点，在这里我们是返回了一个对象，假如我们只是返回一个基本类型的值呢？
	- 再举个例子：
		- ```
		  function Otaku (name, age) {
		      this.strength = 60;
		      this.age = age;
		  
		      return 'handsome boy';
		  }
		  
		  var person = new Otaku('Kevin', '18');
		  
		  console.log(person.name) // undefined
		  console.log(person.habit) // undefined
		  console.log(person.strength) // 60
		  console.log(person.age) // 18
		  ```
	- 结果完全颠倒过来，这次尽管有返回值，但是相当于没有返回值进行处理。
	- 所以我们还需要判断返回的值是不是一个对象，如果是一个对象，我们就返回这个对象，如果没有，我们该返回什么就返回什么。
	- 再来看第二版的代码，也是最后一版的代码：
	- ```
	  // 第二版的代码
	  function objectFactory() {
	  
	      var obj = new Object(),
	  
	      Constructor = [].shift.call(arguments);
	  
	      obj.__proto__ = Constructor.prototype;
	  
	      var ret = Constructor.apply(obj, arguments);
	  
	      return typeof ret === 'object' ? ret : obj;
	  
	  };
	  ```