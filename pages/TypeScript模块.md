- > https://ts.yayujs.com/handbook/Modules.html
-
- ## 模块（Module）
	- JavaScript 有一个很长的处理模块化代码的历史，TypeScript 从 2012 年开始跟进，现在已经实现支持了很多格式。不过随着时间流逝，社区和 JavaScript 规范已经收敛为名为 ES 模块（或者 ES6 模块）的格式，这也就是我们所知的 `import/export` 语法。
	- ES 模块在 2015 年被添加到 JavaScript 规范中，到 2020 年，大部分的 web 浏览器和 JavaScript 运行环境都已经广泛支持。
	- 本章将覆盖讲解 ES 模块和和它之前流行的前身 CommonJS `module.exports =` 语法，你可以在 [Modules](https://www.typescriptlang.org/docs/handbook/modules.html)章节找到其他的模块模式。
- ## JavaScript 模块是如何被定义的（How JavaScript Modules are Defined）
	- 在 TypeScript 中，就像在 ECMAScript 2015 中 ，任何包含了一个顶层 `import` 或者 `export` 的文件会被认为是一个**模块**。
	- 相对应的，一个没有顶层导入和导出声明的文件会被认为是一个**脚本**，它的内容会在全局范围内可用。
	- [[#green]]==模块会在它自己的作用域，而不是在全局作用域里执行==。这意味着，在一个模块中声明的变量、函数、类等，对于模块之外的代码都是不可见的，除非你显示的导出这些值。
	- 相对应的，要消费一个从另一个的模块导出的值、函数、类、接口等，也需要使用导入的格式先被导入。
- ## TypeScript 中的模块（Modules in TypeScript）
	- 在 TypeScript 中，当写一个基于模块的代码时，有三个主要的事情需要考虑：
		- **语法**：我想导出或者导入该用什么语法？
		- **模块解析**：模块名字（或路径）和硬盘文件之间的关系是什么样的？
		- **模块导出目标**：导出的 JavaScript 模块长什么样？
	- ### ES 模块语法（ES Module Syntax）
	  background-color:: pink
		- 一个文件可以通过 `export default` 声明一个主要的导出：
		- ```
		  // @filename: hello.ts
		  export default function helloWorld() {
		    console.log("Hello, world!");
		  }
		  ```
		- 然后用这种方式导入：
		- ```
		  import hello from "./hello.js";
		  hello();
		  ```
		- 除了默认导出，你可以通过省略 `default` 的 `export` 语法导出不止一个变量和函数：
		- ```
		  // @filename: maths.ts
		  export var pi = 3.14;
		  export let squareTwo = 1.41;
		  export const phi = 1.61;
		   
		  export class RandomNumberGenerator {}
		   
		  export function absolute(num: number) {
		    if (num < 0) return num * -1;
		    return num;
		  }
		  
		  import { pi, phi, absolute } from "./maths.js";
		   
		  console.log(pi);
		  const absPhi = absolute(phi);
		  // const absPhi: number
		  ```
	- ### 附加导入语法（Additional Import Syntax）
	  background-color:: pink
		- 一个导入也可以使用类似于 `import {old as new}` 的格式被重命名：
		- ```
		  import { pi as π } from "./maths.js";
		   
		  console.log(π);
		  // (alias) var π: number
		  // import π
		  ```
		- 你可以接受所有的导出对象，然后使用 `* as name` 把它们放入一个单独的命名空间：
		- ```
		  // @filename: app.ts
		  import * as math from "./maths.js";
		   
		  console.log(math.pi);
		  const positivePhi = math.absolute(math.phi);
		  
		  // const positivePhi: number
		  ```
		- 你可以通过 `import "./file"` 导入一个文件，这不会引用任何变量到你当前模块：
		- ```
		  // @filename: app.ts
		  import "./maths.js";
		   
		  console.log("3.14");
		  ```
		- > 在这个例子中， `import` 什么也没干，然而，`math.ts` 的所有代码都会执行，触发一些影响其他对象的副作用（side-effects）。
	- ### TypeScript 具体的 ES 模块语法（TypeScript Specific ES Module Syntax）
	  background-color:: blue
		- **类型**可以像 JavaScript 值那样，使用相同的语法被导出和导入：
		- ```
		  // @filename: animal.ts
		  export type Cat = { breed: string; yearOfBirth: number };
		   
		  export interface Dog {
		    breeds: string[];
		    yearOfBirth: number;
		  }
		   
		  // @filename: app.ts
		  import { Cat, Dog } from "./animal.js";
		  type Animals = Cat | Dog;
		  ```
		- TypeScript 已经在两个方面拓展了 `import` 语法，方便类型导入：
		- #### 导入类型（import type）
		  background-color:: green
			- ```
			  // @filename: animal.ts
			  export type Cat = { breed: string; yearOfBirth: number };
			  // 'createCatName' cannot be used as a value because it was imported using 'import type'.
			  export type Dog = { breeds: string[]; yearOfBirth: number };
			  export const createCatName = () => "fluffy";
			   
			  // @filename: valid.ts
			  import type { Cat, Dog } from "./animal.js";
			  export type Animals = Cat | Dog;
			   
			  // @filename: app.ts
			  import type { createCatName } from "./animal.js";
			  const name = createCatName();
			  ```
		- #### 内置类型导入（Inline type imports）
		  background-color:: green
			- TypeScript 4.5 也允许单独的导入，你需要使用 `type` 前缀 ，表明被导入的是一个类型：
			- ```
			  // @filename: app.ts
			  import { createCatName, type Cat, type Dog } from "./animal.js";
			   
			  export type Animals = Cat | Dog;
			  const name = createCatName();
			  ```
			- 这些可以让一个非 TypeScript 编译器比如 Babel、swc 或者 esbuild 知道什么样的导入可以被安全移除。
			- 导入类型和内置类型导入的区别在于一个是导入语法，一个是仅仅导入类型。
		-