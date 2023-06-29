- > https://ts.yayujs.com/handbook/ConditionalTypes.html
-
- ## 条件类型（Conditional Types）
	- 很多时候，我们需要基于输入的值来决定输出的值，同样我们也需要基于输入的值的类型来决定输出的值的类型。**条件类型（Conditional types）**就是用来帮助我们描述输入类型和输出类型之间的关系。
	- ```
	  interface Animal {
	    live(): void;
	  }
	  
	  interface Dog extends Animal {
	    woof(): void;
	  }
	   
	  type Example1 = Dog extends Animal ? number : string;     
	  // type Example1 = number
	   
	  type Example2 = RegExp extends Animal ? number : string;     
	  // type Example2 = string
	  
	  ```
	- 条件类型的写法有点类似于 JavaScript 中的条件表达式（`condition ? trueExpression : falseExpression` ）：
		- `SomeType extends OtherType ? TrueType : FalseType;`
	- 单从这个例子，可能看不出条件类型有什么用，但当搭配泛型使用的时候就很有用了，让我们拿下面的 `createLabel` 函数为例：
	- ```
	  interface IdLabel {
	    id: number /* some fields */;
	  }
	  interface NameLabel {
	    name: string /* other fields */;
	  }
	   
	  function createLabel(id: number): IdLabel;
	  function createLabel(name: string): NameLabel;
	  function createLabel(nameOrId: string | number): IdLabel | NameLabel;
	  function createLabel(nameOrId: string | number): IdLabel | NameLabel {
	    throw "unimplemented";
	  }
	  
	  ```
	- 这里使用了函数重载，描述了 `createLabel` 是如何基于输入值的类型不同而做出不同的决策，返回不同的类型。[[#red]]==注意这样一些事情：==
		- 如果一个库不得不在一遍又一遍的遍历 API 后做出相同的选择，它会变得非常笨重。
		  logseq.order-list-type:: number
		- 我们不得不创建三个重载，一个是为了处理明确知道的类型，我们为每一种类型都写了一个重载（这里一个是 `string`，一个是 `number`），一个则是为了通用情况 （接收一个 `string | number`）。而如果增加一种新的类型，重载的数量将呈指数增加。
		  logseq.order-list-type:: number
	- 其实我们完全可以用把逻辑写在条件类型中：
	- ```
	  type NameOrId<T extends number | string> = T extends number
	    ? IdLabel
	    : NameLabel;
	  ```
- ## 条件类型约束 （Conditional Type Constraints）
	- 通常，使用条件类型会为我们提供一些新的信息。正如使用 **类型保护（type guards）** 可以 **收窄类型（narrowing）** 为我们提供一个更加具体的类型，条件类型的 `true` 分支也会进一步约束泛型，举个例子：
	- ```
	  type MessageOf<T> = T["message"];
	  // Type '"message"' cannot be used to index type 'T'.
	  ```
	- TypeScript 报错是因为 `T` 不知道有一个名为 `message` 的属性。我们可以约束 `T`，这样 TypeScript 就不会再报错：
	- ```
	  type MessageOf<T extends { message: unknown }> = T["message"];
	   
	  interface Email {
	    message: string;
	  }
	   
	  type EmailMessageContents = MessageOf<Email>;
	  // type EmailMessageContents = string
	  
	  ```
- ## 在条件类型里推断（Inferring Within Conditional Types）
	- 条件类型提供了 `infer` 关键词，可以从正在比较的类型中推断类型，然后在 `true` 分支里引用该推断结果。借助 `infer`，我们修改下 `Flatten` 的实现，不再借助索引访问类型“手动”的获取出来：
	- ```
	  ```