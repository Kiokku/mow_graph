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
	-