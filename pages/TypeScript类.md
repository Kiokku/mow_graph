- > https://ts.yayujs.com/handbook/Class.html
-
- ## 类（Classes）
	- TypeScript 完全支持 ES2015 引入的 `class` 关键字。
- ## 类成员（Class Members）
	- 这是一个最基本的类，一个空类：
	- ```
	  class Point {}
	  ```
	- ### 字段（Fields）
	  background-color:: pink
		- 一个字段声明会创建一个公共（public）可写入（writeable）的属性：
		- ```
		  class Point {
		    x: number;
		    y: number;
		  }
		   
		  const pt = new Point();
		  pt.x = 0;
		  pt.y = 0;
		  ```
		- 注意：类型注解是可选的，如果没有指定，会隐式的设置为 `any`。
		- 字段可以设置初始值（initializers）：
		- ```
		  class Point {
		    x = 0;
		    y = 0;
		  }
		   
		  const pt = new Point();
		  // Prints 0, 0
		  console.log(`${pt.x}, ${pt.y}`);
		  ```
		- 就像 `const` 、`let` 和 `var` ，一个类属性的初始值会被用于推断它的类型:
		- ```
		  const pt = new Point();
		  pt.x = "0";
		  // Type 'string' is not assignable to type 'number'.
		  ```
		- #### --strictPropertyInitialization
		  background-color:: green
			- [strictPropertyInitialization](https://www.typescriptlang.org/tsconfig#strictPropertyInitialization)选项控制了类字段是否需要在构造函数里初始化。
			- ```
			  class BadGreeter {
			    name: string;
			    // Property 'name' has no initializer and is not definitely assigned in the constructor.
			  }
			  
			  class GoodGreeter {
			    name: string;
			   
			    constructor() {
			      this.name = "hello";
			    }
			  }
			  ```
			- 注意，[[#blue]]==字段需要在构造函数自身进行初始化==。TypeScript 并不会分析构造函数里你调用的方法，进而判断初始化的值，因为一个派生类也许会覆盖这些方法并且初始化成员失败：
			- ```
			  class BadGreeter {
			    name: string;
			    // Property 'name' has no initializer and is not definitely assigned in the constructor.
			    setName(): void {
			      this.name = '123'
			    }
			    constructor() {
			      this.setName();
			    }
			  }
			  ```
			- 如果你执意要通过其他方式初始化一个字段，而不是在构造函数里（举个例子，引入外部库为你补充类的部分内容），你可以使用明确赋值断言操作符（definite assignment assertion operator） `!`:
			- ```
			  class OKGreeter {
			    // Not initialized, but no error
			    name!: string;
			  }
			  ```
	- ### `readonly`
	  background-color:: pink
		- 字段可以添加一个 `readonly` 前缀修饰符，这会[[#red]]==阻止在**构造函数**之外的赋值==。
		- ```
		  class Greeter {
		    readonly name: string = "world";
		   
		    constructor(otherName?: string) {
		      if (otherName !== undefined) {
		        this.name = otherName;
		      }
		    }
		   
		    err() {
		      this.name = "not ok";
		  		// Cannot assign to 'name' because it is a read-only property.
		    }
		  }
		  
		  const g = new Greeter();
		  g.name = "also not ok";
		  // Cannot assign to 'name' because it is a read-only property.
		  ```
	- ### 构造函数（Constructors）
	  background-color:: pink
		- 类的构造函数跟函数非常类似，你可以使用带类型注解的参数、默认值、重载等。
		- ```
		  class Point {
		    x: number;
		    y: number;
		   
		    // Normal signature with defaults
		    constructor(x = 0, y = 0) {
		      this.x = x;
		      this.y = y;
		    }
		  }
		  
		  ```
		- ```
		  class Point {
		    // Overloads
		    constructor(x: number, y: string);
		    constructor(s: string);
		    constructor(xs: any, y?: any) {
		      // TBD
		    }
		  }
		  ```
		- 但类构造函数签名与函数签名之间也有一些区别：
			- 构造函数不能有类型参数（关于类型参数，回想下泛型里的内容），这些属于外层的类声明。
			- 构造函数不能有[返回类型注解](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#return-type-annotations)，因为总是返回类实例类型。
		- #### Super 调用（Super Calls）
		  background-color:: green
			- 就像在 JavaScript 中，如果你有一个基类，你需要在使用任何 `this.` 成员之前，先在构造函数里调用 `super()`。
			- ```
			  class Base {
			    k = 4;
			  }
			   
			  class Derived extends Base {
			    constructor() {
			      // Prints a wrong value in ES5; throws exception in ES6
			      console.log(this.k);
			  		// 'super' must be called before accessing 'this' in the constructor of a derived class.
			      super();
			    }
			  }
			  ```
	- ### 方法（Methods）
	  background-color:: pink
		- 类中的函数属性被称为方法。方法跟函数、构造函数一样，使用相同的类型注解。
		- ```
		  class Point {
		    x = 10;
		    y = 10;
		   
		    scale(n: number): void {
		      this.x *= n;
		      this.y *= n;
		    }
		  }
		  ```
	- ### Getters / Setter
	  background-color:: pink
		- ```
		  class C {
		    _length = 0;
		    get length() {
		      return this._length;
		    }
		    set length(value) {
		      this._length = value;
		    }
		  }
		  ```
		- TypeScript 对存取器有一些特殊的推断规则：
			- 如果 `get` 存在而 `set` 不存在，属性会被自动设置为 `readonly`
			- 如果 setter 参数的类型没有指定，它会被推断为 getter 的返回类型
			- getters 和 setters 必须有相同的成员可见性
	- ### 索引签名（Index Signatures）
	  background-color:: pink
		- 类可以声明索引签名，它和对象类型的索引签名是一样的：
		- ```
		  class MyClass {
		    [s: string]: boolean | ((s: string) => boolean);
		   
		    check(s: string) {
		      return this[s] as boolean;
		    }
		  }
		  ```
- ## 类继承（Class Heritage）
	- ### `implements`   语句（ `implements`   Clauses）
	  background-color:: pink
		- 你可以使用 `implements` 语句检查一个类是否满足一个特定的 `interface`。如果一个类没有正确的实现(implement)它，TypeScript 会报错：
		- ```
		  interface Pingable {
		    ping(): void;
		  }
		   
		  class Sonar implements Pingable {
		    ping() {
		      console.log("ping!");
		    }
		  }
		   
		  class Ball implements Pingable {
		    // Class 'Ball' incorrectly implements interface 'Pingable'.
		    // Property 'ping' is missing in type 'Ball' but required in type 'Pingable'.
		    pong() {
		      console.log("pong!");
		    }
		  }
		  ```
		- 类也可以实现多个接口，比如 `class C implements A, B {`
		- #### 注意事项（Cautions）
		  background-color:: green
			- `implements` 语句仅仅检查类是否按照接口类型实现，但它并不会改变类的类型或者方法的类型。一个常见的错误就是以为 `implements` 语句会改变类的类型——然而实际上它并不会：
			- ```
			  interface Checkable {
			    check(name: string): boolean;
			  }
			   
			  class NameChecker implements Checkable {
			    check(s) {
			   		// Parameter 's' implicitly has an 'any' type.
			      // Notice no error here
			      return s.toLowercse() === "ok";
			      				// any
			  }
			  ```
			- 类似的，[[#red]]==实现一个有可选属性的接口，并不会创建这个属性==：
			- ```
			  interface A {
			    x: number;
			    y?: number;
			  }
			  class C implements A {
			    x = 0;
			  }
			  const c = new C();
			  c.y = 10;
			  
			  // Property 'y' does not exist on type 'C'.
			  ```
	- ### `extends`   语句（ `extends`   Clauses）
	  background-color:: pink
		- 类可以 `extend` 一个基类。[[#green]]==一个派生类有基类所有的属性和方法，还可以定义额外的成员。==
		- #### 覆写属性（Overriding Methods）
		  background-color:: green
			- 一个派生类可以覆写一个基类的字段或属性。你可以使用 `super` 语法访问基类的方法。
			- [[#blue]]==TypeScript 强制要求派生类总是它的基类的子类型。==
			- 举个例子，这是一个合法的覆写方法的方式：
			- ```
			  class Base {
			    greet() {
			      console.log("Hello, world!");
			    }
			  }
			   
			  class Derived extends Base {
			    greet(name?: string) {
			      if (name === undefined) {
			        super.greet();
			      } else {
			        console.log(`Hello, ${name.toUpperCase()}`);
			      }
			    }
			  }
			   
			  const d = new Derived();
			  d.greet();
			  d.greet("reader");
			  ```
			- 派生类需要遵循着它的基类的实现。而且通过一个基类引用指向一个派生类实例，这是非常常见并合法的：
			- ```
			  // Alias the derived instance through a base class reference
			  const b: Base = d;
			  // No problem
			  b.greet();
			  ```
			- 但是如果 `Derived` 不遵循 `Base` 的约定实现呢？
			- ```
			  class Base {
			    greet() {
			      console.log("Hello, world!");
			    }
			  }
			   
			  class Derived extends Base {
			    // Make this parameter required
			    greet(name: string) {
			  	// Property 'greet' in type 'Derived' is not assignable to the same property in base type 'Base'.
			    // Type '(name: string) => void' is not assignable to type '() => void'.
			      console.log(`Hello, ${name.toUpperCase()}`);
			    }
			  }
			  ```
			- 即便我们忽视错误编译代码，这个例子也会运行错误：
			- ```
			  const b: Base = new Derived();
			  // Crashes because "name" will be undefined
			  b.greet();
			  ```
		- #### 初始化顺序（Initialization Order）
		  background-color:: green
			- 有些情况下，JavaScript 类初始化的顺序会让你感到很奇怪，让我们看这个例子：
			- ```
			  class Base {
			    name = "base";
			    constructor() {
			      console.log("My name is " + this.name);
			    }
			  }
			   
			  class Derived extends Base {
			    name = "derived";
			  }
			   
			  // Prints "base", not "derived"
			  const d = new Derived();
			  ```
			- 类初始化的顺序，就像在 JavaScript 中定义的那样：
				- 1. 基类字段初始化
				  2. 基类构造函数运行
				  3. 派生类字段初始化
				  4. 派生类构造函数运行
- ## 成员可见性（Member Visibility）
	- 你可以使用 TypeScript 控制某个方法或者属性是否对类以外的代码可见。
	- ### `public`
	  background-color:: pink
		- 类成员**默认**的可见性为 `public`，一个 `public` 的成员可以在任何地方被获取：
		- ```
		  class Greeter {
		    public greet() {
		      console.log("hi!");
		    }
		  }
		  const g = new Greeter();
		  g.greet();
		  ```
	- ### `protected`
	  background-color:: pink
		- `protected` 成员仅仅对子类可见：
		- ```
		  class Greeter {
		    public greet() {
		      console.log("Hello, " + this.getName());
		    }
		    protected getName() {
		      return "hi";
		    }
		  }
		   
		  class SpecialGreeter extends Greeter {
		    public howdy() {
		      // OK to access protected member here
		      console.log("Howdy, " + this.getName());
		    }
		  }
		  const g = new SpecialGreeter();
		  g.greet(); // OK
		  g.getName();
		  
		  // Property 'getName' is protected and only accessible within class 'Greeter' and its subclasses.
		  ```
		- #### 受保护成员的公开（Exposure of protected members）
		  background-color:: green
			- 派生类需要遵循基类的实现，但是依然可以选择公开拥有更多能力的基类子类型，这就包括[[#green]]==让一个 `protected` 成员变成 `public`==：
			- ```
			  class Base {
			    protected m = 10;
			  }
			  class Derived extends Base {
			    // No modifier, so default is 'public'
			    m = 15;
			  }
			  const d = new Derived();
			  console.log(d.m); // OK
			  ```
		- #### 交叉等级受保护成员访问（Cross-hierarchy protected access）
		  background-color:: green
			- 不同的 OOP 语言在通过一个基类引用是否可以合法的获取一个 `protected` 成员是有争议的。
			- ```
			  class Base {
			    protected x: number = 1;
			  }
			  class Derived1 extends Base {
			    protected x: number = 5;
			  }
			  class Derived2 extends Base {
			    f1(other: Derived2) {
			      other.x = 10;
			    }
			    f2(other: Base) {
			      other.x = 10;
			  		// Property 'x' is protected and only accessible through an instance of class 'Derived2'. This is an instance of class 'Base'.
			    }
			  }
			  ```
			- 在 Java 中，这是合法的，而 C# 和 C++ 认为这段代码是不合法的。
			- TypeScript 站在 C# 和 C++ 这边。因为 `Derived2` 的 `x` 应该只有从 `Derived2` 的子类访问才是合法的，而 `Derived1` 并不是它们中的一个。此外，如果通过 `Derived1` 访问 `x` 是不合法的，通过一个基类引用访问也应该是不合法的。
	- ### `private`
	  background-color:: pink
		- `private` 有点像 `protected` ，但是[[#red]]==不允许访问成员，即便是子类==。
		- ```
		  class Base {
		    private x = 0;
		  }
		  const b = new Base();
		  // Can't access from outside the class
		  console.log(b.x);
		  // Property 'x' is private and only accessible within class 'Base'.
		  
		  class Derived extends Base {
		    showX() {
		      // Can't access in subclasses
		      console.log(this.x);
		  		// Property 'x' is private and only accessible within class 'Base'.
		    }
		  }
		  ```
- ## 静态成员（Static Members）
	- 类可以有静态成员，静态成员跟类实例没有关系，可以通过类本身访问到：
	- ```
	  class MyClass {
	    static x = 0;
	    static printX() {
	      console.log(MyClass.x);
	    }
	  }
	  console.log(MyClass.x);
	  MyClass.printX();
	  ```
	- 静态成员同样可以使用 `public` `protected` 和 `private` 这些可见性修饰符：
	- ```
	  class MyClass {
	    private static x = 0;
	  }
	  console.log(MyClass.x);
	  // Property 'x' is private and only accessible within class 'MyClass'.
	  ```
	- 静态成员也可以被继承：
	- ```
	  class Base {
	    static getGreeting() {
	      return "Hello world";
	    }
	  }
	  class Derived extends Base {
	    myGreeting = Derived.getGreeting();
	  }
	  ```
	- ### 特殊静态名称（Special Static Names）
	  background-color:: pink
		- 类本身是函数，而覆写 `Function` 原型上的属性通常认为是不安全的，因此不能使用一些固定的静态名称，函数属性像 `name`、`length`、`call` 不能被用来定义 `static` 成员：
		- ```
		  class S {
		    static name = "S!";
		    // Static property 'name' conflicts with built-in property 'Function.name' of constructor function 'S'.
		  }
		  ```
	- ### 为什么没有静态类？（Why No Static Classes?）
	  background-color:: pink
		- TypeScript（和 JavaScript） 并没有名为静态类（static class）的结构，但是像 C# 和 Java 有。
		- 所谓静态类，指的是作为类的静态成员存在于某个类的内部的类。比如这种：
		- ```
		  // java
		  public class OuterClass {
		    private static String a = "1";
		  	static class InnerClass {
		    	private int b = 2;
		    }
		  }
		  ```
		- [[#red]]==静态类之所以存在是因为这些语言强迫所有的数据和函数都要在一个类内部，但这个限制在 TypeScript 中并不存在，==所以也没有静态类的需要。一个只有一个单独实例的类，在 JavaScript/TypeScript 中，完全可以使用普通的对象替代。
- ## 类静态块（static Blocks in Classes）
	- 静态块允许你写一系列有自己作用域的语句，也可以获取类里的私有字段。这意味着我们可以安心的写初始化代码：正常书写语句，无变量泄漏，还可以完全获取类中的属性和方法。
	- ```
	  class Foo {
	      static #count = 0;
	   
	      get count() {
	          return Foo.#count;
	      }
	   
	      static {
	          try {
	              const lastInstances = loadLastInstances();
	              Foo.#count += lastInstances.length;
	          }
	          catch {}
	      }
	  }
	  ```
- ## 泛型类（Generic Classes）
	- 类跟接口一样，也可以写泛型。当使用 `new` 实例化一个泛型类，它的类型参数的推断跟函数调用是同样的方式：
	- ```
	  class Box<Type> {
	    contents: Type;
	    constructor(value: Type) {
	      this.contents = value;
	    }
	  }
	   
	  const b = new Box("hello!");
	  // const b: Box<string>
	  ```
- ## 类运行时的   `this` （this at Runtime in Classes）
	- TypeScript 并不会更改 JavaScript 运行时的行为，并且 JavaScript 有时会出现一些奇怪的运行时行为。
	- 就比如 JavaScript 处理 `this` 就很奇怪：
	- ```
	  class MyClass {
	    name = "MyClass";
	    getName() {
	      return this.name;
	    }
	  }
	  const c = new MyClass();
	  const obj = {
	    name: "obj",
	    getName: c.getName,
	  };
	   
	  // Prints "obj", not "MyClass"
	  console.log(obj.getName());
	  ```
	- 默认情况下，函数中 `this` 的值取决于函数是如何被调用的。在这个例子中，因为函数通过 `obj` 被调用，所以 `this` 的值是 `obj` 而不是类实例。
	- TypeScript 提供了一些方式缓解或者阻止这种错误。
	- ### 箭头函数（Arrow Functions）
	  background-color:: pink
		- 如果你有一个函数，经常在被调用的时候丢失 `this` 上下文，使用一个箭头函数或许更好些。
		- ```
		  class MyClass {
		    name = "MyClass";
		    getName = () => {
		      return this.name;
		    };
		  }
		  const c = new MyClass();
		  const g = c.getName;
		  // Prints "MyClass" instead of crashing
		  // 箭头函数无法传递调用者的this，向外层作用域寻找this.name，即class MyClass
		  console.log(g());
		  ```
		- 这里有几点需要注意下：
			- `this` 的值在运行时是正确的，即使 TypeScript 不检查代码
			- 这会使用更多的内存，因为每一个类实例都会拷贝一遍这个函数。
			- 你不能在派生类使用 `super.getName` ，因为在原型链中并没有入口可以获取基类方法。
	- ### `this`   参数（this parameters）
	  background-color:: pink
		- 在 TypeScript 方法或者函数的定义中，第一个参数且名字为 `this` 有特殊的含义。该参数会在编译的时候被抹除：
		- ```
		  // TypeScript input with 'this' parameter
		  function fn(this: SomeType, x: number) {
		    /* ... */
		  }
		  
		  // JavaScript output
		  function fn(x) {
		    /* ... */
		  }
		  ```
		- TypeScript 会检查一个有 `this` 参数的函数在调用时是否有一个正确的上下文。不像上个例子使用箭头函数，[[#green]]==我们可以给方法定义添加一个 `this` 参数，静态强制方法被正确调用：==
		- ```
		  class MyClass {
		    name = "MyClass";
		    getName(this: MyClass) {
		      return this.name;
		    }
		  }
		  const c = new MyClass();
		  // OK
		  c.getName();
		   
		  // Error, would crash
		  const g = c.getName;
		  console.log(g());
		  // The 'this' context of type 'void' is not assignable to method's 'this' of type 'MyClass'.
		  ```
		- 这个方法也有一些注意点，正好跟箭头函数相反：
			- JavaScript 调用者依然可能在没有意识到它的时候错误使用类方法
			  每个类一个函数，而不是每一个类实例一个函数
			  基类方法定义依然可以通过 `super` 调用
- ## `this`   类型（this Types）
	- 在类中，有一个特殊的名为 `this` 的类型，会动态的引用当前类的类型，让我们看下它的用法：
	- ```
	  class Box {
	    contents: string = "";
	    set(value: string) {
	  	// (method) Box.set(value: string): this
	      this.contents = value;
	      return this;
	    }
	  }
	  ```
	- 这里，TypeScript 推断 `set` 的返回类型为 `this` 而不是 `Box` 。让我们写一个 `Box` 的子类：
	- ```
	  class ClearableBox extends Box {
	    clear() {
	      this.contents = "";
	    }
	  }
	   
	  const a = new ClearableBox();
	  const b = a.set("hello");
	  
	  // const b: ClearableBox
	  ```
	- 你也可以在参数类型注解中使用 `this` ：
	- ```
	  class Box {
	    content: string = "";
	    sameAs(other: this) {
	      return other.content === this.content;
	    }
	  }
	  ```
- ## 参数属性（Parameter Properties）
	- TypeScript 提供了特殊的语法，可以把一个构造函数参数转成一个同名同值的类属性。这些就被称为参数属性（parameter properties）。你可以通过在构造函数参数前添加一个可见性修饰符 `public` `private` `protected` 或者 `readonly` 来创建参数属性，最后这些类属性字段也会得到这些修饰符：
	- ```
	  class Params {
	    constructor(
	      public readonly x: number,
	      protected y: number,
	      private z: number
	    ) {
	      // No body necessary
	    }
	  }
	  const a = new Params(1, 2, 3);
	  console.log(a.x);
	  // (property) Params.x: number
	  
	  console.log(a.z);
	  // Property 'z' is private and only accessible within class 'Params'.
	  ```
- ## 类表达式（Class Expressions）
	- 类表达式跟类声明非常类似，唯一不同的是类表达式不需要一个名字，尽管我们可以通过绑定的标识符进行引用：
	- ```
	  const someClass = class<Type> {
	    content: Type;
	    constructor(value: Type) {
	      this.content = value;
	    }
	  };
	   
	  const m = new someClass("Hello, world");  
	  // const m: someClass<string>
	  ```
- ## 抽象类和成员（abstract Classes and Members）
	- TypeScript 中，类、方法、字段都可以是抽象的（abstract）。
	- 抽象方法或者抽象字段是不提供实现的。这些成员必须存在在一个抽象类中，这个抽象类也不能直接被实例化。
	- 抽象类的作用是作为子类的基类，让子类实现所有的抽象成员。当一个类没有任何抽象成员，他就会被认为是具体的（concrete）。
	- ```
	  abstract class Base {
	    abstract getName(): string;
	   
	    printName() {
	      console.log("Hello, " + this.getName());
	    }
	  }
	   
	  const b = new Base();
	  // Cannot create an instance of an abstract class.
	  ```
	- 我们不能使用 `new` 实例 `Base` 因为它是抽象类。我们需要写一个派生类，并且实现抽象成员。
	- ```
	  class Derived extends Base {
	    getName() {
	      return "world";
	    }
	  }
	   
	  const d = new Derived();
	  d.printName();
	  ```
	- [[#red]]==注意，如果我们忘记实现基类的抽象成员，我们会得到一个报错。==
	- ### 抽象构造签名（Abstract Construct Signatures）
	  background-color:: pink
		- ```
		  function greet(ctor: new () => Base) {
		    const instance = new ctor();
		    instance.printName();
		  }
		  greet(Derived);
		  greet(Base);
		  
		  // Argument of type 'typeof Base' is not assignable to parameter of type 'new () => Base'.
		  // Cannot assign an abstract constructor type to a non-abstract constructor type.
		  ```
- ## 类之间的关系（Relationships Between Classes）
	- 大部分时候，TypeScript 的类跟其他类型一样，会被结构性比较。
	- 举个例子，这两个类可以用于替代彼此，因为它们结构是相等的：
	- ```
	  class Point1 {
	    x = 0;
	    y = 0;
	  }
	   
	  class Point2 {
	    x = 0;
	    y = 0;
	  }
	   
	  // OK
	  const p: Point1 = new Point2();
	  ```
	- 类似的还有，类的子类型之间可以建立关系，即使没有明显的继承：
	- ```
	  class Person {
	    name: string;
	    age: number;
	  }
	   
	  class Employee {
	    name: string;
	    age: number;
	    salary: number;
	  }
	   
	  // OK
	  const p: Person = new Employee();
	  ```
	- **空类**没有任何成员。在一个结构化类型系统中，没有成员的类型通常是任何其他类型的父类型。所以如果你写一个空类（只是举例，你可不要这样做），任何东西都可以用来替换它：
	- ```
	  class Empty {}
	   
	  function fn(x: Empty) {
	    // can't do anything with 'x', so I won't
	  }
	   
	  // All OK!
	  fn(window);
	  fn({});
	  fn(fn);
	  ```