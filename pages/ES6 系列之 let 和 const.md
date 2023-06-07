- > https://github.com/mqyqingfeng/Blog/issues/82
-
- ## 块级作用域的出现
	- 通过 var 声明的变量存在变量提升的特性：
	- ```
	  if (condition) {
	      var value = 1;
	  }
	  console.log(value);
	  ```
	- 因为变量提升的原因，代码相当于：
	- ```
	  var value;
	  if (condition) {
	  	value = 1;
	  }
	  console.log(value);
	  ```
	- 除此之外，在 for 循环中：
	- ```
	  for (var i = 0; i < 10; i++) {
	      ...
	  }
	  console.log(i); // 10
	  ```
	- 为了加强对变量生命周期的控制，ECMAScript 6 引入了[[#blue]]==块级作用域==。
	- 块级作用域存在于：
		- 1. 函数内部
		  2. 块中(字符 { 和 } 之间的区域)
- ## let 和 const
	- **1.不会被提升**
		- ```
		  if (false) {
		      let value = 1;
		  }
		  console.log(value); // Uncaught ReferenceError: value is not defined
		  ```
	- **2.重复声明报错**
		- ```
		  var value = 1;
		  let value = 2; // Uncaught SyntaxError: Identifier 'value' has already been declared
		  ```
	- **3.不绑定全局作用域**
		- 当在全局作用域中使用 var 声明的时候，会创建一个新的全局变量作为全局对象的属性。
		- ```
		  var value = 1;
		  console.log(window.value); // 1
		  ```
		- 然而 let 和 const 不会：
		- ```
		  let value = 1;
		  console.log(window.value); // undefined
		  ```
		- 再来说下 let 和 const 的区别：
		- const 用于[[#blue]]==声明常量==，其值一旦被设定不能再被修改，否则会报错。
		- const 声明不允许修改绑定，但[[#blue]]==允许修改值==。这意味着当用 const 声明对象时：
		- ```
		  const data = {
		      value: 1
		  }
		  
		  // 没有问题
		  data.value = 2;
		  data.num = 3;
		  
		  // 报错
		  data = {}; // Uncaught TypeError: Assignment to constant variable.
		  ```
- ## 临时死区
	- 临时死区(Temporal Dead Zone)，简写为 TDZ。
	- let 和 const 声明的变量不会被提升到作用域顶部，如果在声明之前访问这些变量，会导致报错：
	- ```
	  console.log(typeof value); // Uncaught ReferenceError: value is not defined
	  let value = 1;
	  ```
	- 这是因为 JavaScript 引擎在扫描代码发现变量声明时，要么将它们提升到作用域顶部(遇到 var 声明)，要么将声明放在 TDZ 中(遇到 let 和 const 声明)。访问 TDZ 中的变量会触发运行时错误。只有执行过变量声明语句后，变量才会从 TDZ 中移出，然后方可访问。
- ## 循环中的块级作用域
	- ```
	  var funcs = [];
	  for (var i = 0; i < 3; i++) {
	      funcs[i] = function () {
	          console.log(i);
	      };
	  }
	  funcs[0](); // 3
	  ```
	- ES6 的 let 为这个问题提供了新的解决方法：
	- ```
	  var funcs = [];
	  for (let i = 0; i < 3; i++) {
	      funcs[i] = function () {
	          console.log(i);
	      };
	  }
	  funcs[0](); // 0
	  ```
	- 问题在于，上面讲了 let 不提升，不能重复声明，**不能绑定全局作用域**等等特性，可是为什么在这里就能正确打印出 i 值呢？
	- 如果要追究这个问题，就要抛弃掉之前所讲的这些特性！这是因为 let 声明在循环内部的行为是标准中专门定义的，不一定就与 let 的不提升特性有关，其实，在早期的 let 实现中就不包含这一行为。
	- 我们查看[ECMAScript 规范第 13.7.4.7 节](http://www.ecma-international.org/ecma-262/6.0/#sec-for-statement-runtime-semantics-labelledevaluation)
	- [[#blue]]==在 for 循环中使用 let 和 var，底层会使用不同的处理方式。==
	- 简单的来说，就是在 `for (let i = 0; i < 3; i++)` 中，即圆括号之内建立一个隐藏的作用域，这就可以解释为什么:
		- ```
		  for (let i = 0; i < 3; i++) {
		    let i = 'abc';
		    console.log(i);
		  }
		  // abc
		  // abc
		  // abc
		  ```
	- 然后**每次迭代循环时都创建一个新变量，并以之前迭代中同名变量的值将其初始化**。这样对于下面这样一段代码
		- ```
		  var funcs = [];
		  for (let i = 0; i < 3; i++) {
		      funcs[i] = function () {
		          console.log(i);
		      };
		  }
		  funcs[0](); // 0
		  ```
		- 就相当于：
		- ```
		  // 伪代码
		  (let i = 0) {
		      funcs[0] = function() {
		          console.log(i)
		      };
		  }
		  
		  (let i = 1) {
		      funcs[1] = function() {
		          console.log(i)
		      };
		  }
		  
		  (let i = 2) {
		      funcs[2] = function() {
		          console.log(i)
		      };
		  };
		  ```
		- 当执行函数的时候，根据词法作用域就可以找到正确的值，其实你也可以理解为 let 声明模仿了闭包的做法来简化循环过程。
- ## 循环中的 let 和 const
  id:: 64804fa6-09b1-4a4f-bc21-7bcebb12c060
	- 如果我们把 let 改成 const 呢？
	- ```
	  var funcs = [];
	  for (const i = 0; i < 10; i++) {
	      funcs[i] = function () {
	          console.log(i);
	      };
	  }
	  funcs[0](); // Uncaught TypeError: Assignment to constant variable.
	  ```
	- [[#blue]]==结果会是报错==，因为虽然我们每次都创建了一个新的变量，然而我们却在迭代中尝试修改 const 的值，所以最终会报错。
	- **for in 循环**
	- ```
	  var funcs = [], object = {a: 1, b: 1, c: 1};
	  for (var key in object) {
	      funcs.push(function(){
	          console.log(key)
	      });
	  }
	  
	  funcs[0]() // 'c'
	  ```
	- 那如果把 var 改成 let 或者 const 呢？
	- 使用 let，结果自然会是 'a'，const 呢？ 报错还是 'a'?
	- [[#blue]]==结果是正确打印 'a'，这是因为在 for in 循环中，每次迭代不会修改已有的绑定，而是会创建一个新的绑定。==
- ## Babel
	- 在 Babel 中是如何编译 let 和 const 的呢？我们来看看编译后的代码：
	- ```
	  let value = 1;
	  ```
	- 编译为:
	- ```
	  var value = 1;
	  ```
	- 我们可以看到 Babel 直接将 let 编译成了 var，如果是这样的话，那么我们来写个例子：
	- ```
	  if (false) {
	      let value = 1;
	  }
	  console.log(value); // Uncaught ReferenceError: value is not defined
	  ```
	- 如果还是直接编译成 var，打印的结果肯定是 undefined，然而 Babel 很聪明，它编译成了：
	- ```
	  if (false) {
	      var _value = 1;
	  }
	  console.log(value);
	  ```
	-