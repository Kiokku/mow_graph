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
-