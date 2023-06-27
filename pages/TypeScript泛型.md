- > https://ts.yayujs.com/handbook/Generics.html
-
- ## 泛型（Generics）
	- 在比如 C# 和 Java 语言中，用来创建可复用组件的工具，我们称之为泛型（generics）。利用泛型，我们可以创建一个支持众多类型的组件，这让用户可以使用自己的类型消费（consume）这些组件。
- ## Generics 初探（Hello World of Generics）
	- 所谓恒等函数，就是一个返回任何传进内容的函数。你也可以把它理解为类似于 `echo` 命令。
	- 不借助泛型，我们也许需要给予恒等函数一个具体的类型：
	- ```
	  function identity(arg: number): number {
	    return arg;
	  }
	  ```
	- 所以我们需要一种可以[[#green]]==捕获参数类型的方式==，然后再用它表示返回值的类型。这里我们用了一个**类型变量（type variable）**，一种用在类型而非值上的特殊的变量。
	- ```
	  function identity<Type>(arg: Type): Type {
	    return arg;
	  }
	  ```
	- 在我们写了一个泛型恒等函数后，我们有两种方式可以调用它。第一种方式是传入所有的参数，包括类型参数：
	- ```
	  let output = identity<string>("myString"); // let output: string
	  ```
	-