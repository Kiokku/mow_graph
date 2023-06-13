- > https://github.com/mqyqingfeng/Blog/issues/85
-
- ## 比较
	- 箭头函数与普通函数的主要区别包括：
	- ### 1.没有 this
	  background-color:: blue
		- **箭头函数没有 this，所以需要通过查找作用域链来确定 this 的值。**
		- [[#green]]==这就意味着如果箭头函数被非箭头函数包含，this 绑定的就是最近一层非箭头函数的 this。==
		- [[#red]]==因为箭头函数没有 this，所以也不能用 call()、apply()、bind() 这些方法改变 this 的指向==
	- ### 2. 没有 arguments
	  background-color:: blue
		- 箭头函数没有自己的 arguments 对象，这不一定是件坏事，因为[[#green]]==箭头函数可以访问外围函数的 arguments 对象==：
		- ```
		  function constant() {
		      return () => arguments[0]
		  }
		  
		  var result = constant(1);
		  console.log(result()); // 1
		  ```
		- 可以通过命名参数或者 rest 参数的形式访问箭头函数参数:
		- ```
		  let nums = (...nums) => nums;
		  ```
	- ### 3. 不能通过 new 关键字调用
	  background-color:: blue
		- JavaScript 函数有两个内部方法：`[[Call]]` 和 `[[Construct]]`。
		- 当通过 new 调用函数时，执行 `[[Construct]]` 方法，创建一个实例对象，然后再执行函数体，将 this 绑定到实例上
		- 当直接调用的时候，执行 `[[Call]]` 方法，直接执行函数体。
		- 箭头函数并没有 `[[Construct]]` 方法，不能被用作构造函数，如果通过 new 的方式调用，会报错。
		- ```
		  var Foo = () => {};
		  var foo = new Foo(); // TypeError: Foo is not a constructor
		  ```
	- ### 4. 没有 new.target
	  background-color:: blue
		- 因为不能使用 new 调用，所以也没有 new.target 值。
		- 关于 new.target，可以参考 [http://es6.ruanyifeng.com/#docs/class#new-target-%E5%B1%9E%E6%80%A7](http://es6.ruanyifeng.com/#docs/class#new-target-%E5%B1%9E%E6%80%A7)
	- ### 5. 没有原型
	  background-color:: blue
		- 由于不能使用 new 调用箭头函数，所以也没有构建原型的需求，于是箭头函数也不存在 prototype 这个属性。
		- ```
		  var Foo = () => {};
		  console.log(Foo.prototype); // undefined
		  ```
	- ### 6. 没有 super
	  background-color:: blue
		- 连原型都没有，自然也不能通过 super 来访问原型的属性，所以箭头函数也是没有 super 的，不过跟 this、arguments、new.target 一样，这些值由外围最近一层非箭头函数决定。
- ## 总结
	- 箭头函数表达式的语法比函数表达式更短，并且不绑定自己的this，arguments，super或 new.target。这些函数表达式最适合用于非方法函数(non-method functions)，并且它们不能用作构造函数。
	- 那么什么是 non-method functions 呢？
	- [[#blue]]==对象属性中的函数就被称之为 method，那么 non-mehtod 就是指不被用作对象属性中的函数==。
	- ```
	  var obj = {
	    i: 10,
	    b: () => console.log(this.i, this),
	    c: function() {
	      console.log( this.i, this)
	    }
	  }
	  obj.b();
	  // undefined Window
	  obj.c();
	  // 10, Object {...}
	  ```
- ## 自执行函数
	- 自执行函数的形式为：
	- ```
	  (function(){
	      console.log(1)
	  })()
	  ```
	- 或者
	- ```
	  (function(){
	      console.log(1)
	  }())
	  ```
	- 利用箭头简化自执行函数的写法：
	- ```
	  (() => {
	      console.log(1)
	  })()
	  ```
	- [[#red]]==但是注意，使用以下这种写法却会报错：==
	- ```
	  (() => {
	      console.log(1)
	  }())
	  ```