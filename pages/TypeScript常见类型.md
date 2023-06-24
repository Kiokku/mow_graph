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
		-