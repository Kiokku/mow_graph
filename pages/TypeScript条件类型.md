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
	-