- > https://ts.yayujs.com/handbook/EverydayType.html
-
- ## 原始类型:   `string` ， `number`   和   `boolean` （The primitives）
	- JavaScript 的三个非常常用的[原始类型](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)：`string`，`number` 和 `boolean`
- ## 数组（Array）
	- 声明一个类似于 `[1, 2, 3]` 的数组类型，你需要用到语法 `number[]` `string[]`
	- > ⚠️ 注意
	  > `[number]` 和 `number[]` 表示不同的意思，参考[元组](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types)章节
- ## `any`
	- TypeScript 有一个特殊的类型，`any`，当你不希望一个值导致类型检查错误的时候，就可以设置为 `any` 。
- ## 变量上的类型注解（Type Annotations on Variables）
	- 当你使用 `const`、`var` 或 `let` 声明一个变量时，你可以选择性的添加一个类型注解，[[#blue]]==显式指定变量的类型==：
	- ```
	  let myName: string = "Alice";
	  ```
	- 不过大部分时候，这不是必须的。因为 TypeScript 会自动推断类型。举个例子，变量的类型可以基于初始值进行推断：
	- ```
	  // No type annotation needed -- 'myName' inferred as type 'string'
	  let myName = "Alice";
	  ```
	- 大部分时候，你不需要学习推断的规则。如果你刚开始使用，[[#green]]==尝试尽可能少的使用类型注解。==
- ## 函数（Function）
	- ### 参数类型注解（Parameter Type Annotations）
	  background-color:: pink
		- 当你声明一个函数的时候，你可以在每个参数后面添加一个类型注解，声明函数可以接受什么类型的参数。
		- ```
		  // Parameter type annotation
		  function greet(name: string) {
		    console.log("Hello, " + name.toUpperCase() + "!!");
		  }
		  ```
	- ### 返回值类型注解（Return Type Annotations）
	  background-color:: pink
		- ```
		  function getFavoriteNumber(): number {
		    return 26;
		  }
		  ```
	- ### 匿名函数（Anonymous Functions）
	  background-color:: pink
		- 匿名函数有一点不同于函数声明，[[#green]]==当 TypeScript 知道一个匿名函数将被怎样调用的时候，匿名函数的参数会被自动的指定类型。==
		- ```
		  // No type annotations here, but TypeScript can spot the bug
		  const names = ["Alice", "Bob", "Eve"];
		   
		  // Contextual typing for function
		  names.forEach(function (s) {
		    console.log(s.toUppercase());
		    // Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
		  });
		   
		  // Contextual typing also applies to arrow functions
		  names.forEach((s) => {
		    console.log(s.toUppercase());
		    // Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
		  });
		  ```
		- 尽管参数 `s` 并没有添加类型注解，但 TypeScript 根据 `forEach` 函数的类型，以及传入的数组的类型，最后推断出了 `s` 的类型。
		- 这个过程被称为**上下文推断（contextual typing）**。
- ## 对象类型（Object Types）
	- 定义一个对象类型，我们只需要简单的列出它的属性和对应的类型。
	- ```
	  // The parameter's type annotation is an object type
	  function printCoord(pt: { x: number; y: number }) {
	    console.log("The coordinate's x value is " + pt.x);
	    console.log("The coordinate's y value is " + pt.y);
	  }
	  printCoord({ x: 3, y: 7 });
	  ```
	- ### 可选属性（Optional Properties）
	  background-color:: pink
		- 对象类型可以指定一些甚至所有的属性为可选的，你只需要在属性名后添加一个 `?` ：
		- ```
		  function printName(obj: { first: string; last?: string }) {
		    // ...
		  }
		  // Both OK
		  printName({ first: "Bob" });
		  printName({ first: "Alice", last: "Alisson" });
		  ```
- ## 联合类型（Union Types）
	- TypeScript 类型系统允许你使用一系列的操作符，基于已经存在的类型构建新的类型。
	- ### 定义一个联合类型（Defining a Union Type）
	  background-color:: pink
		- 一个联合类型是由两个或者更多类型组成的类型，表示值可能是这些类型中的任意一个。这其中每个类型都是联合类型的**成员（members）**。
		- ```
		  function printId(id: number | string) {
		    console.log("Your ID is: " + id);
		  }
		  // OK
		  printId(101);
		  // OK
		  printId("202");
		  // Error
		  printId({ myID: 22342 });
		  // Argument of type '{ myID: number; }' is not assignable to parameter of type 'string | number'.
		  // Type '{ myID: number; }' is not assignable to type 'number'.
		  ```
	- ### 使用联合类型（Working with Union Types）
	  background-color:: pink
		- TypeScript 会要求你做的事情，[[#red]]==必须对每个联合的成员都是有效的。==举个例子，如果你有一个联合类型 `string | number` , 你不能使用只存在 `string` 上的方法。
- ## 类型别名（Type Aliases）
	- `type`
	- ```
	  type Point = {
	    x: number;
	    y: number;
	  };
	   
	  // Exactly the same as the earlier example
	  function printCoord(pt: Point) {
	    console.log("The coordinate's x value is " + pt.x);
	    console.log("The coordinate's y value is " + pt.y);
	  }
	   
	  printCoord({ x: 100, y: 100 });
	  ```
- ## 接口（Interfaces）
	- 接口声明（interface declaration）是命名对象类型的另一种方式：
	- ```
	  interface Point {
	    x: number;
	    y: number;
	  }
	   
	  function printCoord(pt: Point) {
	    console.log("The coordinate's x value is " + pt.x);
	    console.log("The coordinate's y value is " + pt.y);
	  }
	   
	  printCoord({ x: 100, y: 100 });
	  ```
	- ### 类型别名`type`和接口`interface`的不同
	  background-color:: pink
		- [[#blue]]==类型别名和接口非常相似，大部分时候，你可以任意选择使用。==接口的几乎所有特性都可以在 `type` 中使用，[[#red]]==两者最关键的差别在于类型别名本身无法添加新的属性，而**接口是可以扩展的**。==
- ## 类型断言（Type Assertions）
	- `as`：你可以使用类型断言将一个类型指定为一个更具体的类型：
	- ```
	  const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
	  ```
	- 也可以使用尖括号语法 `<Type>` （注意不能在 `.tsx` 文件内使用），是等价的：
	- ```
	  const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
	  ```
	- TypeScript 仅仅[[#red]]==允许类型断言转换为一个更加具体或者更不具体的类型。==这个规则可以阻止一些不可能的强制类型转换，比如：
	- ```
	  const x = "hello" as number;
	  // Conversion of type 'string' to type 'number' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.
	  ```
	- 有的时候，这条规则会显得非常保守，阻止了你原本有效的类型转换。如果发生了这种事情，[[#green]]==你可以使用双重断言，先断言为 `any` （或者是 `unknown`），然后再断言为期望的类型==：
	- ```
	  const a = (expr as any) as T;
	  ```
- ## 字面量类型（Literal Types）
	- 除了常见的类型 `string` 和 `number` ，我们也可以将类型声明为**更具体**的数字或者字符串。
	- 字面量类型本身并没有什么太大用：
	- ```
	  let x: "hello" = "hello";
	  // OK
	  x = "hello";
	  // ...
	  x = "howdy";
	  // Type '"howdy"' is not assignable to type '"hello"'.
	  ```
	- 如果**结合联合类型**，就显得有用多了。举个例子，当函数只能传入一些固定的字符串时：
	- ```
	  function printText(s: string, alignment: "left" | "right" | "center") {
	    // ...
	  }
	  printText("Hello, world", "left");
	  printText("G'day, mate", "centre");
	  // Argument of type '"centre"' is not assignable to parameter of type '"left" | "right" | "center"'.
	  ```
	- ### 字面量推断（Literal Inference）
	  background-color:: pink
		- 当你初始化变量为一个对象的时候，TypeScript 会假设这个对象的属性的值未来会被修改，举个例子，如果你写下这样的代码：
		- ```
		  const obj = { counter: 0 };
		  if (someCondition) {
		    obj.counter = 1;
		  }
		  ```
		- TypeScript 并不会认为 `obj.counter` 之前是 `0`， 现在被赋值为 `1` 是一个错误。换句话说，`obj.counter` 必须是 `number` 类型，但不要求一定是 `0`，因为类型可以决定读写行为。
		- 这也同样应用于字符串:
		- ```
		  declare function handleRequest(url: string, method: "GET" | "POST"): void;
		  
		  const req = { url: "https://example.com", method: "GET" };
		  handleRequest(req.url, req.method);
		  
		  // Argument of type 'string' is not assignable to parameter of type '"GET" | "POST"'.
		  ```
		- 在上面这个例子里，**`req.method` 被推断为 `string`** ，而不是 `"GET"`，因为在创建 `req` 和 调用 `handleRequest` 函数之间，可能还有其他的代码，或许会将 `req.method` 赋值一个新字符串比如 `"Guess"` 。[[#red]]==所以 TypeScript 就报错了。==
		- [[#blue]]==有两种方式可以解决：==
			- 1. 添加一个类型断言改变推断结果：
				- ```
				  // Change 1:
				  const req = { url: "https://example.com", method: "GET" as "GET" };
				  // Change 2
				  handleRequest(req.url, req.method as "GET");
				  ```
	-