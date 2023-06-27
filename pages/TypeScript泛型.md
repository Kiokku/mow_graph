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
	- 这可以引导我们写出[[#blue]]==第一个泛型接口==，让我们使用上个例子中的对象字面量，然后把它的代码移动到接口里：
	- ```
	  interface GenericIdentityFn {
	    <Type>(arg: Type): Type;
	  }
	   
	  function identity<Type>(arg: Type): Type {
	    return arg;
	  }
	   
	  let myIdentity: GenericIdentityFn = identity;
	  ```
	- 有的时候，我们会希望将泛型参数作为整个接口的参数，这可以让我们清楚的知道传入的是什么参数 (举个例子：`Dictionary<string>` 而不是 `Dictionary`)。而且接口里其他的成员也可以看到：
	- ```
	  interface GenericIdentityFn<Type> {
	    (arg: Type): Type;
	  }
	   
	  function identity<Type>(arg: Type): Type {
	    return arg;
	  }
	   
	  let myIdentity: GenericIdentityFn<number> = identity;
	  ```
- ## 泛型类（Generic Classes）
	- 泛型类写法上类似于泛型接口。在类名后面，使用尖括号中 `<>` 包裹住类型参数列表：
	- ```
	  class GenericNumber<NumType> {
	    zeroValue: NumType;
	    add: (x: NumType, y: NumType) => NumType;
	  }
	   
	  let myGenericNumber = new GenericNumber<number>();
	  myGenericNumber.zeroValue = 0;
	  myGenericNumber.add = function (x, y) {
	    return x + y;
	  };
	  ```
- ## 泛型约束（Generic Constraints）
	- 相比于能兼容任何类型，我们更愿意**约束**这个函数。只要类型有这个成员，我们就允许使用它，但必须至少要有这个成员。为此，我们需要列出对 `Type` 约束中的必要条件。
	- 为此，我们需要创建一个接口，用来描述约束。这里，我们创建了一个只有 `.length` 属性的接口，然后我们使用这个接口和 `extends` 关键词实现了约束：
	- ```
	  interface Lengthwise {
	    length: number;
	  }
	   
	  function loggingIdentity<Type extends Lengthwise>(arg: Type): Type {
	    console.log(arg.length); // Now we know it has a .length property, so no more error
	    return arg;
	  }
	  ```
	- 我们需要传入符合约束条件的值：
	- ```
	  loggingIdentity({ length: 10, value: 3 });
	  ```
- ## 在泛型约束中使用类型参数（Using Type Parameters in Generic Constraints）
	- 你可以声明一个类型参数，这个类型参数被其他类型参数约束。
	- 举个例子，我们希望获取一个对象给定属性名的值，为此，我们需要确保我们不会获取 `obj` 上不存在的属性。所以我们在两个类型之间建立一个约束：
	- ```
	  function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
	    return obj[key];
	  }
	   
	  let x = { a: 1, b: 2, c: 3, d: 4 };
	   
	  getProperty(x, "a");
	  getProperty(x, "m");
	  
	  // Argument of type '"m"' is not assignable to parameter of type '"a" | "b" | "c" | "d"'.
	  ```
- ## 在泛型中使用类类型（Using Class Types in Generics）
	- 在 TypeScript 中，当使用工厂模式创建实例的时候，有必要通过他们的构造函数推断出类的类型，举个例子：
	- ```
	  function create<Type>(c: { new (): Type }): Type {
	    return new c();
	  }
	  ```
	- 下面是一个更复杂的例子，使用原型属性推断和约束，构造函数和类实例的关系。
	- ```
	  class BeeKeeper {
	    hasMask: boolean = true;
	  }
	   
	  class ZooKeeper {
	    nametag: string = "Mikle";
	  }
	   
	  class Animal {
	    numLegs: number = 4;
	  }
	   
	  class Bee extends Animal {
	    keeper: BeeKeeper = new BeeKeeper();
	  }
	   
	  class Lion extends Animal {
	    keeper: ZooKeeper = new ZooKeeper();
	  }
	   
	  function createInstance<A extends Animal>(c: new () => A): A {
	    return new c();
	  }
	   
	  createInstance(Lion).keeper.nametag;
	  createInstance(Bee).keeper.hasMask;
	  ```
	-