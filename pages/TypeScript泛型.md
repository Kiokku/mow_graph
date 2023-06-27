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
	- 在我们写了一个泛型恒等函数后，我们有两种方式可以调用它。
		- 第一种方式是传入所有的参数，包括类型参数：
		- ```
		  let output = identity<string>("myString"); // let output: string
		  ```
		- 第二种方式可能更常见一些，这里我们使用了**类型参数推断（type argument inference）**（部分中文文档会翻译为“**类型推论**”），我们希望编译器能基于我们传入的参数自动推断和设置 `Type` 的值。
		- ```
		  let output = identity("myString"); // let output: string
		  ```
- ## 使用泛型类型变量（Working with Generic Type Variables）
	- 现在假设这个函数，使用的是 `Type` 类型的数组而不是 `Type`。因为我们使用的是数组，`.length` 属性肯定存在。我们就可以像创建其他类型的数组一样写：
	- ```
	  function loggingIdentity<Type>(arg: Type[]): Type[] {
	    console.log(arg.length);
	    return arg;
	  }
	  
	  //我们也可以这样写这个例子，效果是一样的
	  function loggingIdentity<Type>(arg: Array<Type>): Array<Type> {
	    console.log(arg.length); // Array has a .length, so no more error
	    return arg;
	  }
	  ```
- ## 泛型类型 (Generic Types)
	- 在这个章节，我们探索函数本身的类型，以及如何创建泛型接口。
	- 泛型函数的形式就跟其他非泛型函数的一样，都需要先列一个类型参数列表，这有点像函数声明：
	- ```
	  function identity<Type>(arg: Type): Type {
	    return arg;
	  }
	   
	  let myIdentity: <Type>(arg: Type) => Type = identity;
	  ```
	- 泛型的类型参数可以使用不同的名字，只要数量和使用方式上一致即可：
	- ```
	  function identity<Type>(arg: Type): Type {
	    return arg;
	  }
	   
	  let myIdentity: <Input>(arg: Input) => Input = identity;
	  ```
	- 我们也可以以对象类型的调用签名的形式，书写这个泛型类型：
	- ```
	  function identity<Type>(arg: Type): Type {
	    return arg;
	  }
	   
	  let myIdentity: { <Type>(arg: Type): Type } = identity;
	  ```
	-
	-