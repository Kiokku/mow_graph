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
	- 在刚才的例子中，有一个细节你可能没有注意到，那就是如果我们打开编译输出的文件，我们会发现文件依然发生了改动。这是不是有点奇怪？`tsc` 明明已经报错了，为什么还要再编译文件？这就要讲到[[#green]]==TypeScript 一个核心的观点：大部分时候，你要比 TypeScript 更清楚你的代码。==
	- 举个例子，假如你正在把你的代码迁移成 TypeScript，这会产生很多类型检查错误，而你不得不为类型检查器处理掉所有的错误，这时候你就要想了，明明之前的代码可以正常工作，TypeScript 为什么要阻止代码正常运行呢？
	- 所以 TypeScript 并不会阻碍你。当然了，你如果想要 TypeScript 更严厉一些，你可以使用 [noEmitOnError](https://www.typescriptlang.org/tsconfig#noEmitOnError)编译选项，试着改下你的 `hello.ts` 文件，然后运行 `tsc`:`tsc --noEmitOnError hello.ts`
- ## 显示类型（Explicit Types）
	- ```
	  function greet(person: string, date: Date) {
	    console.log(`Hello ${person}, today is ${date.toDateString()}!`);
	  }
	  ```
	- 我们所做的就是给 `person` 和 `date` 添加了**类型注解（type annotations）**
- ## 类型抹除（Erased Types）
	- ```
	  "use strict";
	  function greet(person, date) {
	      console.log("Hello " + person + ", today is " + date.toDateString() + "!");
	  }
	  greet("Maddison", new Date());
	  ```
	- [[#red]]==类型注解并不是 JavaScript 的一部分。==所以并没有任何浏览器或者运行环境可以直接运行 TypeScript 代码。这就是为什么 TypeScript 需要一个编译器，它需要将 TypeScript 代码转换为 JavaScript 代码，然后你才可以运行它。所以大部分 TypeScript 独有的代码会被抹除，在这个例子中，像我们的类型注解就全部被抹除了。
- ## 降级（Downleveling）
	- TypeScript 默认转换为 `ES3`，一个 ECMAScript 非常老的版本。我们也可以使用 [target (opens new window)](https://www.typescriptlang.org/tsconfig#target)选项转换为比较新的一些版本，比如执行 `--target es2015` 会转换为 ECMAScript 2015, 这意味着转换后的代码可以在任何支持 ECMAScript 2015 的地方运行。
	- 执行 `tsc --target es2015 hello.ts`
	- >尽管默认的目标是 ES3 版本，但是大多数的浏览器都已经支持 ES2015 了，因此大部分开发者可以安全的指定为 ES2015 或者更新的版本，除非你非要兼容某个问题浏览器。
- ## 严格模式（Strictness）
	- TypeScript 有几个严格模式设置的开关。除非特殊说明，文档里的例子都是在严格模式下写的。CLI 里的 [strict](https://www.typescriptlang.org/tsconfig/#strict)配置项，或者 [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)中的 `"strict": true` 可以同时开启，也可以分开设置。在这些设置里，你最需要了解的是 [noImplicitAny](https://www.typescriptlang.org/tsconfig#noImplicitAny)和 [strictNullChecks](https://www.typescriptlang.org/tsconfig#strictNullChecks)。
	-