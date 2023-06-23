- > https://ts.yayujs.com/handbook/TheBasics.html
-
- ## 基础（The Basics）
	- JavaScript 的每个值执行不同的操作时会有不同的行为。这听起来有点抽象，所以让我们举个例子，假设我们有一个名为 `message` 的变量，试想我们可以做哪些操作：
	- ```
	  // Accessing the property 'toLowerCase'
	  // on 'message' and then calling it
	  message.toLowerCase();
	  // Calling 'message'
	  message();
	  ```
		- `message` 是可调用的吗？
		  `message` 有一个名为 `toLowerCase` 的属性吗？
		  如果有，`toLowerCase` 是可以被调用的吗？
		  如果这些值都可以被调用，它们会返回什么？
	- 类型就是描述什么样的值可以被传递给 `fn`，什么样的值则会导致崩溃。[[#red]]==JavaScript 仅仅提供了**动态类型（dynamic typing）**，这需要你先运行代码然后再看会发生什么。==
	- [[#green]]==替代方案就是使用**静态类型系统（static type system）**，在代码运行之前就预测需要什么样的代码。==
- ## 静态类型检查（Static type-checking）
	- 理想情况下，我们应该有一个工具可以帮助我们，[[#green]]==在代码运行之前就找到错误==。这就是**静态类型检查器**比如 TypeScript 做的事情。**静态类型系统（Static types systems）描述了值应有的结构和行为。**一个像 TypeScript 的类型检查器会利用这个信息，并且在可能会出错的时候告诉我们：
	- ```
	  const message = "hello!";
	   
	  message();
	  
	  // This expression is not callable.
	  // Type 'String' has no call signatures.
	  ```
- ## 非异常失败（Non-exception 失败）
	- [ECMAScript 规范 ](https://tc39.github.io/ecma262/)已经明确的声明了这些异常时的行为，例如规范规定，当调用一个非可调用的东西时应该抛出一个错误。由此你可能认为，如果获取一个对象不存在的属性也应该抛出一个错误，但是 JavaScript 并不会这样，它不报错，还返回值 `undefined`。
	- ```
	  const user = {
	    name: "Daniel",
	    age: 26,
	  };
	  user.location; // returns undefined
	  ```
	- [[#blue]]==静态类型需要标记出哪些代码是一个错误，哪怕实际生效的 JavaScript 并不会立刻报错。==
	- ```
	  const user = {
	    name: "Daniel",
	    age: 26,
	  };
	   
	  user.location;
	  // Property 'location' does not exist on type '{ name: string; age: number; }'.
	  ```
- ## 类型工具（Types for Tooling）
	- 类型检查器因为有类型信息，可以检查比如说是否正确获取了一个变量的属性。也正是因为有这个信息，它也可以在你输入的时候，列出你可能想要使用的属性。
	- 这意味着 TypeScript 对你编写代码也很有帮助，核心的类型检查器不仅可以提供错误信息，还可以提供代码补全功能。
	- 还可以支持“快速修复”功能，即自动的修复错误，重构成组织清晰的代码。同时也支持导航功能，比如跳转到变量定义的地方，或者找到一个给定的变量所有的引用。
- ## `tsc`   TypeScript 编译器（tsc，the TypeScript compiler）
- ## 报错时仍产出文件（Emitting with Errors）
	-