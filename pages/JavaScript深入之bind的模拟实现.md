- > [https://github.com/mqyqingfeng/Blog/issues/12](https://github.com/mqyqingfeng/Blog/issues/12)
-
- ## bind
	- 一句话介绍 bind:
	- > bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。(来自于 MDN )
	- 由此我们可以首先得出 bind 函数的两个特点：
		- 返回一个函数
		- 可以传入参数
- ## 返回函数的模拟实现
	- 从第一个特点开始，我们举个例子：
		- ```
		  var foo = {
		      value: 1
		  };
		  
		  function bar() {
		      console.log(this.value);
		  }
		  
		  // 返回了一个函数
		  var bindFoo = bar.bind(foo); 
		  
		  bindFoo(); // 1
		  ```
	- 关于指定 this 的指向，我们可以使用 call 或者 apply 实现
	- ```
	  // 第一版
	  Function.prototype.bind2 = function (context) {
	      var self = this;
	      return function () {
	          return self.apply(context);
	      }
	  
	  }
	  ```
	- ```
	  var foo = {
	      value: 1
	  };
	  
	  function bar() {
	  	return this.value;
	  }
	  
	  var bindFoo = bar.bind(foo);
	  
	  console.log(bindFoo()); // 1
	  ```
- ## 传参的模拟实现
	- 接下来看第二点，可以传入参数。这个就有点让人费解了，我在 bind 的时候，是否可以传参呢？我在执行 bind 返回的函数的时候，可不可以传参呢？让我们看个例子：
		- ```
		  var foo = {
		      value: 1
		  };
		  
		  function bar(name, age) {
		      console.log(this.value);
		      console.log(name);
		      console.log(age);
		  
		  }
		  
		  var bindFoo = bar.bind(foo, 'daisy');
		  bindFoo('18');
		  // 1
		  // daisy
		  // 18
		  ```
	- 函数需要传 name 和 age 两个参数，竟然还可以在 bind 的时候，只传一个 name，在执行返回的函数的时候，再传另一个参数 age!
	- 我们用 arguments 进行处理：
	- ```
	  // 第二版
	  Function.prototype.bind2 = function (context) {
	  
	      var self = this;
	      // 获取bind2函数从第二个参数到最后一个参数
	      var args = Array.prototype.slice.call(arguments, 1);
	  
	      return function () {
	          // 这个时候的arguments是指bind返回的函数传入的参数
	          var bindArgs = Array.prototype.slice.call(arguments);
	          return self.apply(context, args.concat(bindArgs));
	      }
	  
	  }
	  ```
- ## 构造函数效果的模拟实现
	- 完成了这两点，最难的部分到啦！因为 bind 还有一个特点，就是
	- > 一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。
	- 也就是说[[#blue]]==当 bind 返回的函数作为构造函数的时候，bind 时指定的 this 值会失效，但传入的参数依然生效==。举个例子：
		- ```
		  var value = 2;
		  
		  var foo = {
		      value: 1
		  };
		  
		  function bar(name, age) {
		      this.habit = 'shopping';
		      console.log(this.value);
		      console.log(name);
		      console.log(age);
		  }
		  
		  bar.prototype.friend = 'kevin';
		  
		  var bindFoo = bar.bind(foo, 'daisy');
		  
		  var obj = new bindFoo('18');
		  // undefined
		  // daisy
		  // 18
		  console.log(obj.habit);
		  console.log(obj.friend);
		  // shopping
		  // kevin
		  ```
		- 注意：尽管在全局和 foo 中都声明了 value 值，最后依然返回了 undefind，说明绑定的 this 失效了，如果大家了解 new 的模拟实现，就会知道这个时候的 this 已经指向了 obj。
	- 所以我们可以通过修改返回的函数的原型来实现，让我们写一下：
	- ```
	  // 第三版
	  Function.prototype.bind2 = function (context) {
	      var self = this;
	      var args = Array.prototype.slice.call(arguments, 1);
	  
	      var fBound = function () {
	          var bindArgs = Array.prototype.slice.call(arguments);
	          // 当作为构造函数时，this 指向实例，此时结果为 true，将绑定函数的 this 指向该实例，可以让实例获得来自绑定函数的值
	          // 以上面的是 demo 为例，如果改成 `this instanceof fBound ? null : context`，实例只是一个空对象，将 null 改成 this ，实例会具有 habit 属性
	          // 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context
	          return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
	      }
	      // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值
	      fBound.prototype = this.prototype;
	      return fBound;
	  }
	  ```
- ## 构造函数效果的优化实现
	- 但是在这个写法中，我们直接将 fBound.prototype = this.prototype，我们直接修改 fBound.prototype 的时候，也会直接修改绑定函数的 prototype。这个时候，我们可以通过一个空函数来进行中转：
	- ```
	  // 第四版
	  Function.prototype.bind2 = function (context) {
	  
	      var self = this;
	      var args = Array.prototype.slice.call(arguments, 1);
	  
	      var fNOP = function () {};
	  
	      var fBound = function () {
	          var bindArgs = Array.prototype.slice.call(arguments);
	          return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs));
	      }
	  
	      fNOP.prototype = this.prototype;
	      fBound.prototype = new fNOP();
	      // 相当于 fBound.prototype = Object.create(this.prototype)
	      return fBound;
	  }
	  ```
- ## 几个小问题
	- **1.调用 bind 的不是函数咋办？**
		- 不行，我们要报错！
		- ```
		  if (typeof this !== "function") {
		    throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
		  }
		  ```
	- **2.我要在线上用**
		- ```
		  Function.prototype.bind = Function.prototype.bind || function () {
		      ……
		  };
		  ```
- ## 最终代码
	- ```
	  Function.prototype.bind2 = function (context) {
	  
	      if (typeof this !== "function") {
	        throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
	      }
	  
	      var self = this;
	      var args = Array.prototype.slice.call(arguments, 1);
	  
	      var fBound = function () {
	          var bindArgs = Array.prototype.slice.call(arguments);
	          return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
	      }
	  
	      fBound.prototype = Object.create(this.prototype);
	      return fBound;
	  }
	  ```
- ## SoftBind
  id:: 651ef3d1-3dc9-4511-89a8-16f99d72d479
	- `softBind` 是相对原生 `bind` 而言一个更灵活的绑定 `this` 的功能。原生 `bind` 有一个弊端：被绑定后的新函数无法再更改 `this`。
	- `softBind` 改进了 `bind`，使得对新函数的 `call`、`apply` 调用表现正常。
	- ```
	  Function.prototype.softBind = function(obj, ...rest) {
	      const self = this;
	      
	      const bound = function(...args) {
	        const o = !this || this === (window || global) ? obj : this
	        return self.apply(o, [...rest, ...args])
	      }
	  
	      bound.prototype = Object.create(fn.prototype)
	      return bound;
	  }
	  ```