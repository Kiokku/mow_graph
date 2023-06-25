- > https://ts.yayujs.com/handbook/MoreOnFunctions.html
-
- ## 函数类型表达式（Function Type Expressions）
	- 最简单描述一个函数的方式是使用**函数类型表达式（function type expression）。**它的写法有点类似于箭头函数：
	- ```
	  function greeter(fn: (a: string) => void) {
	    fn("Hello, World");
	  }
	   
	  function printToConsole(s: string) {
	    console.log(s);
	  }
	   
	  greeter(printToConsole);
	  ```
	- 我们也可以使用类型别名（type alias）定义一个函数类型：
	- ```
	  type GreetFunction = (a: string) => void;
	  function greeter(fn: GreetFunction) {
	    // ...
	  }
	  ```
- ## 调用签名（Call Signatures）
	- 在 JavaScript 中，[[#green]]==函数除了可以被调用，自己也是可以有属性值的。==然而上一节讲到的函数类型表达式并不能支持声明属性，如果我们想描述一个带有属性的函数，我们可以在一个对象类型中写一个**调用签名（call signature）**。
	- ```
	  type DescribableFunction = {
	    description: string;
	    (someArg: number): boolean;
	  };
	  function doSomething(fn: DescribableFunction) {
	    console.log(fn.description + " returned " + fn(6));
	  }
	  ```
- ## 构造签名 （Construct Signatures）
	- JavaScript 函数也可以使用 `new` 操作符调用，当被调用的时候，TypeScript 会认为这是一个**构造函数(constructors)**，因为他们会产生一个新对象。你可以写一个构造签名，方法是在调用签名前面加一个 `new` 关键词：
	- ```
	  type SomeConstructor = {
	    new (s: string): SomeObject;
	  };
	  function fn(ctor: SomeConstructor) {
	    return new ctor("hello");
	  }
	  ```
- ## 泛型函数 （Generic Functions）
	- 我们经常需要写这种函数，即函数的输出类型依赖函数的输入类型，或者两个输入的类型以某种形式相互关联。让我们考虑这样一个函数，它返回数组的第一个元素：