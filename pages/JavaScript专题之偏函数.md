- > https://github.com/mqyqingfeng/Blog/issues/43
-
- ## 定义
	- 维基百科中对偏函数 (Partial application) 的定义为：
	- > In computer science, partial application (or partial function application) refers to the process of fixing a number of arguments to a function, producing another function of smaller arity.
	  >
	  > 在计算机科学中，局部应用（偏函数）是指固定一个函数的一些参数，然后产生另一个更小元的函数。
	- 举个简单的例子：
	- ```
	  function add(a, b) {
	      return a + b;
	  }
	  
	  // 执行 add 函数，一次传入两个参数即可
	  add(1, 2) // 3
	  
	  // 假设有一个 partial 函数可以做到局部应用
	  var addOne = partial(add, 1);
	  
	  addOne(2) // 3
	  ```
- ## 柯里化与局部应用
	- **柯里化**是将一个多参数函数转换成多个单参数函数，也就是将一个 n 元函数转换成 n 个一元函数。
	- **局部应用**则是固定一个函数的一个或者多个参数，也就是将一个 n 元函数转换成一个 n - x 元函数。
	- 如果说两者有什么关系的话，引用 [functional-programming-jargon](https://github.com/hemanth/functional-programming-jargon#partial-application) 中的描述就是：
	- > Curried functions are automatically partially applied.
- ## partial
	- ### 第一版
	  background-color:: pink
		- ```
		  // 第一版
		  // 似曾相识的代码
		  function partial(fn) {
		      var args = [].slice.call(arguments, 1);
		      return function() {
		          var newArgs = args.concat([].slice.call(arguments));
		          return fn.apply(this, newArgs);
		      };
		  };
		  ```
		- 我们来写个 demo 验证下 this 的指向：
		- ```
		  function add(a, b) {
		      return a + b + this.value;
		  }
		  
		  // var addOne = add.bind(null, 1);
		  var addOne = partial(add, 1);
		  
		  var value = 1;
		  var obj = {
		      value: 2,
		      addOne: addOne
		  }
		  obj.addOne(2); // ???
		  // 使用 bind 时，结果为 4
		  // 使用 partial 时，结果为 5
		  ```
	- ### 第二版
	  background-color:: pink
		- 然而正如 curry 函数可以使用占位符一样，我们希望 partial 函数也可以实现这个功能，我们再来写第二版：
		- ```
		  // 第二版
		  var _ = {};
		  
		  function partial(fn) {
		      var args = [].slice.call(arguments, 1);
		      return function() {
		          var position = 0, len = args.length;
		          for(var i = 0; i < len; i++) {
		              args[i] = args[i] === _ ? arguments[position++] : args[i]
		          }
		          while(position < arguments.length) args.push(arguments[position++]);
		          return fn.apply(this, args);
		      };
		  };
		  ```
		- 我们验证一下：
		- ```
		  var subtract = function(a, b) { return b - a; };
		  subFrom20 = partial(subtract, _, 20);
		  subFrom20(5);
		  ```
- ## 写在最后
	- 值得注意的是：underscore 和 lodash 都提供了 partial 函数，但只有 lodash 提供了 curry 函数。