- > https://ts.yayujs.com/handbook/MoreOnFunctions.html
-
- ## 函数类型表达式（Function Type Expressions）
	- 最简单描述一个函数的方式是使用**函数类型表达式（function type expression）。**它的写法有点类似于箭头函数：
	- ```
	  function greeter(fn: (a: string) => void) {
	    fn("Hello, World");
	  }
	   
	  function printToConsole(s: string) {
	    console.log(s);
	  }
	   
	  greeter(printToConsole);
	  ```
	- 我们也可以使用类型别名（type alias）定义一个函数类型：
	- ```
	  type GreetFunction = (a: string) => void;
	  function greeter(fn: GreetFunction) {
	    // ...
	  }
	  ```
- ## 调用签名（Call Signatures）
	- 在 JavaScript 中，[[#green]]==函数除了可以被调用，自己也是可以有属性值的。==然而上一节讲到的函数类型表达式并不能支持声明属性，如果我们想描述一个带有属性的函数，我们可以在一个对象类型中写一个**调用签名（call signature）**。
	- ```
	  type DescribableFunction = {
	    description: string;
	    (someArg: number): boolean;
	  };
	  function doSomething(fn: DescribableFunction) {
	    console.log(fn.description + " returned " + fn(6));
	  }
	  ```
- ## 构造签名 （Construct Signatures）
	- JavaScript 函数也可以使用 `new` 操作符调用，当被调用的时候，TypeScript 会认为这是一个**构造函数(constructors)**，因为他们会产生一个新对象。你可以写一个构造签名，方法是在调用签名前面加一个 `new` 关键词：
	- ```
	  type SomeConstructor = {
	    new (s: string): SomeObject;
	  };
	  function fn(ctor: SomeConstructor) {
	    return new ctor("hello");
	  }
	  ```
- ## 泛型函数 （Generic Functions）
	- 我们经常需要写这种函数，即函数的输出类型依赖函数的输入类型，或者两个输入的类型以某种形式相互关联。让我们考虑这样一个函数，它返回数组的第一个元素：
	- ```
	  function firstElement(arr: any[]) {
	    return arr[0];
	  }
	  ```
	- > 注意此时函数返回值的类型是 `any`，如果能返回第一个元素的具体类型就更好了。
	- 在 TypeScript 中，[[#blue]]==**泛型**就是被用来描述两个值之间的对应关系。==我们需要在函数签名里声明一个**类型参数 (type parameter)**：
	- ```
	  function firstElement<Type>(arr: Type[]): Type | undefined {
	    return arr[0];
	  }
	  ```
	- > 通过给函数添加一个类型参数 `Type`，并且在两个地方使用它，我们就在函数的输入(即数组)和函数的输出(即返回值)之间创建了一个关联。
	- 现在当我们调用它，一个更具体的类型就会被判断出来：
	- ```
	  // s is of type 'string'
	  const s = firstElement(["a", "b", "c"]);
	  // n is of type 'number'
	  const n = firstElement([1, 2, 3]);
	  // u is of type undefined
	  const u = firstElement([]);
	  ```
	- ### 推断（Inference）
	  background-color:: pink
		- 在上面的例子中，我们没有明确指定 `Type` 的类型，类型是被 TypeScript 自动推断出来的。
	- ### 约束（Constraints）
	  background-color:: pink
		- 有的时候，我们想关联两个值，但只能操作值的一些固定字段，这种情况，我们可以使用**约束（constraint）**对类型参数进行限制。
		- 让我们写一个函数，函数返回两个值中更长的那个。为此，我们需要保证传入的值有一个 `number` 类型的 `length` 属性。我们使用 [[#green]]==`extends` 语法==来约束函数参数：
		- ```
		  function longest<Type extends { length: number }>(a: Type, b: Type) {
		    if (a.length >= b.length) {
		      return a;
		    } else {
		      return b;
		    }
		  }
		   
		  // longerArray is of type 'number[]'
		  const longerArray = longest([1, 2], [1, 2, 3]);
		  // longerString is of type 'alice' | 'bob'
		  const longerString = longest("alice", "bob");
		  // Error! Numbers don't have a 'length' property
		  const notOK = longest(10, 100);
		  // Argument of type 'number' is not assignable to parameter of type '{ length: number; }'.
		  ```
	- ### 泛型约束实战（Working with Constrained Values）
	  background-color:: green
		- 这是一个使用[[#red]]==泛型约束常出现的错误==：
		- ```
		  function minimumLength<Type extends { length: number }>(
		    obj: Type,
		    minimum: number
		  ): Type {
		    if (obj.length >= minimum) {
		      return obj;
		    } else {
		      return { length: minimum };
		      // Type '{ length: number; }' is not assignable to type 'Type'.
		      // '{ length: number; }' is assignable to the constraint of type 'Type', but 'Type' could be instantiated with a different subtype of constraint '{ length: number; }'.
		    }
		  }
		  ```
		- > 而这其中的问题就在于[[#blue]]==函数理应返回与传入参数相同类型的对象==，而不仅仅是符合约束的对象。我们可以写出这样一段反例：
		- ```
		  // 'arr' gets value { length: 6 }
		  const arr = minimumLength([1, 2, 3], 6);
		  // and crashes here because arrays have
		  // a 'slice' method, but not the returned object!
		  console.log(arr.slice(0));
		  ```
	- ### 声明类型参数 （Specifying Type Arguments）
	  background-color:: pink
		- TypeScript 通常能自动推断泛型调用中传入的类型参数，但也并不能总是推断出。举个例子，有这样一个合并两个数组的函数：
		- ```
		  function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
		    return arr1.concat(arr2);
		  }
		  
		  const arr = combine([1, 2, 3], ["hello"]);
		  // Type 'string' is not assignable to type 'number'.
		  ```
		- 如果你执意要这样做，你可以[[#green]]==手动指定 `Type`==：
		- ```
		  const arr = combine<string | number>([1, 2, 3], ["hello"]);
		  ```
	- ### 写一个好的泛型函数的一些建议
	  background-color:: blue
		- #### 类型参数下移（Push Type Parameters Down）
		  background-color:: green
			- ```
			  function firstElement1<Type>(arr: Type[]) {
			    return arr[0];
			  }
			   
			  function firstElement2<Type extends any[]>(arr: Type) {
			    return arr[0];
			  }
			   
			  // a: number (good)
			  const a = firstElement1([1, 2, 3]);
			  // b: any (bad)
			  const b = firstElement2([1, 2, 3]);
			  ```
			- > 第一眼看上去，两个函数可太相似了，但是第一个函数的写法可比第二个函数好太多了。第一个函数可以推断出返回的类型是 `number`，但第二个函数推断的返回类型却是 `any`，因为 TypeScript 不得不用约束的类型来推断 `arr[0]` 表达式，而[[#red]]==不是等到函数调用的时候再去推断这个元素==。
		- #### 使用更少的类型参数 (Use Fewer Type Parameters)
		  background-color:: green
			- ```
			  function filter1<Type>(arr: Type[], func: (arg: Type) => boolean): Type[] {
			    return arr.filter(func);
			  }
			   
			  function filter2<Type, Func extends (arg: Type) => boolean>(
			    arr: Type[],
			    func: Func
			  ): Type[] {
			    return arr.filter(func);
			  }
			  ```
			- > 我们创建了一个并没有关联两个值的类型参数 `Func`，这是一个危险信号，因为它意味着调用者不得不毫无理由的手动指定一个额外的类型参数。`Func` 什么也没做，却导致函数更难阅读和推断。
		- #### 类型参数应该出现两次 （Type Parameters Should Appear Twice）
		  background-color:: green
			- ```
			  function greet<Str extends string>(s: Str) {
			    console.log("Hello, " + s);
			  }
			   
			  greet("world");
			  
			  // 其实我们可以如此简单的写这个函数
			  function greet(s: string) {
			    console.log("Hello, " + s);
			  }
			  
			  ```
- ## 可选参数（Optional Parameters）
	- JavaScript 中的函数经常会被传入非固定数量的参数，举个例子：`number` 的 `toFixed` 方法就支持传入一个可选的参数，我们可以使用 `?` 表示这个参数是可选的：
	- ```
	  function f(n: number) {
	    console.log(n.toFixed()); // 0 arguments
	    console.log(n.toFixed(3)); // 1 argument
	  }
	  
	  function f(x?: number) {
	    // ...
	  }
	  f(); // OK
	  f(10); // OK
	  ```
	- ### 回调中的可选参数（Optional Parameters in Callbacks）
	  background-color:: pink
		- 在你学习过可选参数和函数类型表达式后，你很容易在包含了回调函数的函数中，犯下面这种错误：
		- ```
		  function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
		    for (let i = 0; i < arr.length; i++) {
		      callback(arr[i], i);
		    }
		  }
		  ```
		- 将 `index?`作为一个可选参数，本意是希望下面这些调用是合法的：
		- ```
		  myForEach([1, 2, 3], (a) => console.log(a));
		  myForEach([1, 2, 3], (a, i) => console.log(a, i));
		  ```
		- 但 TypeScript 并不会这样认为，TypeScript 认为想表达的是回调函数可能只会被传入一个参数，换句话说，`myForEach` 函数也可能是这样的：
		- ```
		  function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
		    for (let i = 0; i < arr.length; i++) {
		      // I don't feel like providing the index today
		      callback(arr[i]);
		    }
		  }
		  ```
		- 那如何修改呢？不设置为可选参数其实就可以。
		- 在 JavaScript 中，如果你调用一个函数的时候，传入了比需要更多的参数，额外的参数就会被忽略。TypeScript 也是同样的做法。
		- > 当你写一个回调函数的类型时,**不要写一个可选参数**, 除非你真的打算调用函数的时候不传入实参。
- ## 函数重载（Function Overloads）
	- 一些 JavaScript 函数在调用的时候可以**传入不同数量和类型的参数**。举个例子。你可以写一个函数，返回一个日期类型 `Date`，这个函数接收一个时间戳（一个参数）或者一个 月/日/年 的格式 (三个参数)。
	- 在 TypeScript 中，我们可以通过写**重载签名 (overlaod signatures)** 说明一个函数的不同调用方法。 我们需要写一些函数签名 (通常两个或者更多)，然后再写函数体的内容：
	- ```
	  function makeDate(timestamp: number): Date;
	  function makeDate(m: number, d: number, y: number): Date;
	  function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
	    if (d !== undefined && y !== undefined) {
	      return new Date(y, mOrTimestamp, d);
	    } else {
	      return new Date(mOrTimestamp);
	    }
	  }
	  const d1 = makeDate(12345678);
	  const d2 = makeDate(5, 5, 5);
	  const d3 = makeDate(1, 3);
	  
	  // No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
	  ```
	- > 在这个例子中，我们写了两个函数重载，一个接受一个参数，另外一个接受三个参数。[[#green]]==前面两个函数签名被称为重载签名 (overload signatures)==。
	  >
	  > 然后，我们写了一个兼容签名的函数实现，我们称之为[[#green]]==实现签名 (implementation signature)== ，[[#red]]==但这个签名不能被直接调用。尽管我们在函数声明中，在一个必须参数后，声明了两个可选参数，它依然不能被传入两个参数进行调用。==
	- ### 重载签名和实现签名（Overload Signatures and the Implementation Signature）
	  background-color:: pink
		- 这是一个常见的困惑。大家常会这样写代码，但是又不理解为什么会报错：·
		- ```
		  function fn(x: string): void;
		  function fn() {
		    // ...
		  }
		  // Expected to be able to call with zero arguments
		  fn();
		  Expected 1 arguments, but got 0.
		  ```
		- > 实现签名对外界来说是**不可见**的。当写一个重载函数的时候，你应该总是需要来两个或者更多的签名在实现签名之上。