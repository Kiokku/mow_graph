- > https://ts.yayujs.com/handbook/KeyofTypeOperator.html
-
- ## `keyof`   类型操作符
	- 对一个对象类型使用 `keyof` 操作符，会返回该对象属性名组成的一个字符串或者数字字面量的联合。这个例子中的类型 P 就等同于 "x" | "y"：
	- ```
	  type Point = { x: number; y: number };
	  type P = keyof Point;
	  
	  // type P = "x" | "y"
	  ```
	- 但如果这个类型有一个 `string` 或者 `number` 类型的索引签名，`keyof` 则会直接返回这些类型：
	- ```
	  type Arrayish = { [n: number]: unknown };
	  type A = keyof Arrayish;
	  // type A = number
	  
	  type Mapish = { [k: string]: boolean };
	  type M = keyof Mapish;
	  // type M = string | number
	  ```
	- 注意在这个例子中，`M` 是 `string | number`，这是因为 [[#red]]==JavaScript 对象的属性名会被强制转为一个字符串==，所以 `obj[0]` 和 `obj["0"]` 是一样的。
- ## 数字字面量联合类型
	- ```
	  const NumericObject = {
	    [1]: "冴羽一号",
	    [2]: "冴羽二号",
	    [3]: "冴羽三号"
	  };
	  
	  type result = keyof typeof NumericObject
	  
	  // typeof NumbericObject 的结果为：
	  // {
	  //   1: string;
	  //   2: string;
	  //   3: string;
	  // }
	  // 所以最终的结果为：
	  // type result = 1 | 2 | 3
	  ```
- ## Symbol
	- ```
	  const sym1 = Symbol();
	  const sym2 = Symbol();
	  const sym3 = Symbol();
	  
	  const symbolToNumberMap = {
	    [sym1]: 1,
	    [sym2]: 2,
	    [sym3]: 3,
	  };
	  
	  type KS = keyof typeof symbolToNumberMap; // typeof sym1 | typeof sym2 | typeof sym3
	  ```
- ## 类和接口
	- 对类使用 `keyof`：
	- ```
	  // 例子一
	  class Person {
	    name: "冴羽"
	  }
	  
	  type result = keyof Person;
	  // type result = "name"
	  
	  // 例子二
	  class Person {
	    [1]: string = "冴羽";
	  }
	  
	  type result = keyof Person;
	  // type result = 1
	  ```
	- 对接口使用 `keyof`：
	- ```
	  interface Person {
	    name: "string";
	  }
	  
	  type result = keyof Person;
	  // type result = "name"
	  ```
	-
	-