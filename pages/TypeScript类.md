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
	-