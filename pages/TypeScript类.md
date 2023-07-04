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
			- 注意，字段需要在构造函数自身进行初始化。TypeScript 并不会分析构造函数里你调用的方法，进而判断初始化的值，因为一个派生类也许会覆盖这些方法并且初始化成员失败：