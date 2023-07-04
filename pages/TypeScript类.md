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
			-