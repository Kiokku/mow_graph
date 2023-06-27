- > https://ts.yayujs.com/handbook/ObjectTypes.html
-
- ## 对象类型（Object types）
	- 对象类型可以是**匿名**的：
	- ```
	  function greet(person: { name: string; age: number }) {
	    return "Hello " + person.name;
	  }
	  ```
	- 也可以使用**接口**进行定义：
	- ```
	  interface Person {
	    name: string;
	    age: number;
	  }
	   
	  function greet(person: Person) {
	    return "Hello " + person.name;
	  }
	  ```
	- 或者通过**类型别名**：
	- ```
	  type Person = {
	    name: string;
	    age: number;
	  };
	   
	  function greet(person: Person) {
	    return "Hello " + person.name;
	  }
	  ```
- ## 属性修饰符（Property Modifiers）
	- 对象类型中的每个属性可以说明它的**类型**、属性**是否可选**、属性**是否只读**等信息。
	- ### 可选属性（Optional Properties）
	  background-color:: pink
		- `?`:
		- ```
		  interface PaintOptions {
		    shape: Shape;
		    xPos?: number;
		    yPos?: number;
		  }
		   
		  function paintShape(opts: PaintOptions) {
		    // ...
		  }
		   
		  const shape = getShape();
		  paintShape({ shape });
		  paintShape({ shape, xPos: 100 });
		  paintShape({ shape, yPos: 100 });
		  paintShape({ shape, xPos: 100, yPos: 100 });
		  ```
	- ### `readonly`   属性（readonly Properties）
		- 在 TypeScript 中，属性可以被标记为 `readonly`，这不会改变任何运行时的行为，但在类型检查的时候，一个标记为 `readonly`的属性是不能被写入的。
		- ```
		  interface SomeType {
		    readonly prop: string;
		  }
		   
		  function doSomething(obj: SomeType) {
		    // We can read from 'obj.prop'.
		    console.log(`prop has the value '${obj.prop}'.`);
		   
		    // But we can't re-assign it.
		    obj.prop = "hello";
		    // Cannot assign to 'prop' because it is a read-only property.
		  }
		  
		  ```
		- 不过使用 `readonly` 并不意味着一个值就完全是不变的，亦或者说，内部的内容是不能变的。`readonly` 仅仅表明属性本身是不能被重新写入的。
		- ```
		  interface Home {
		    readonly resident: { name: string; age: number };
		  }
		   
		  function visitForBirthday(home: Home) {
		    // We can read and update properties from 'home.resident'.
		    console.log(`Happy birthday ${home.resident.name}!`);
		    home.resident.age++;
		  }
		   
		  function evict(home: Home) {
		    // But we can't write to the 'resident' property itself on a 'Home'.
		    home.resident = {
		    // Cannot assign to 'resident' because it is a read-only property.
		      name: "Victor the Evictor",
		      age: 42,
		    };
		  }
		  ```
	- ### 索引签名（Index Signatures）
	  background-color:: pink
		- 有的时候，你不能提前知道一个类型里的所有属性的名字，但是你知道这些值的特征。
		- 这种情况，你就可以用一个**索引签名 (index signature)** 来描述可能的值的类型，举个例子：
		- ```
		  interface StringArray {
		    [index: number]: string;
		  }
		   
		  const myArray: StringArray = getStringArray();
		  const secondItem = myArray[1]; // const secondItem: string
		  
		  ```
		- 一个索引签名的属性类型必须是 `string` 或者是 `number`。但数字索引的返回类型一定要是字符索引返回类型的子类型。这是因为当使用一个数字进行索引的时候，JavaScript 实际上把它转成了一个字符串。
		- [[#green]]==所有的属性要匹配索引签名的**返回类型**==。这是因为一个声明类似于 `obj.property` 的字符串索引，跟 `obj["property"]`是一样的。在下面的例子中，`name` 的类型并不匹配字符串索引的类型，所以类型检查器会给出报错：
		- ```
		  interface NumberDictionary {
		    [index: string]: number;
		   
		    length: number; // ok
		    name: string;
		  	// Property 'name' of type 'string' is not assignable to 'string' index type 'number'.
		  }
		  ```
- ## 属性继承（Extending Types）
	- 有时我们需要一个比其他类型更具体的类型。举个例子，假设我们有一个 `BasicAddress` 类型用来描述在美国邮寄信件和包裹的所需字段。
	- ```
	  interface BasicAddress {
	    name?: string;
	    street: string;
	    city: string;
	    country: string;
	    postalCode: string;
	  }
	  
	  ```
	- 这在一些情况下已经满足了，但同一个地址的建筑往往还有不同的单元号，我们可以再写一个 `AddressWithUnit`，这样写固然可以，但为了加一个字段，就是要完全的拷贝一遍。
	- 我们可以改成继承 `BasicAddress`的方式来实现：
	- ```
	  interface BasicAddress {
	    name?: string;
	    street: string;
	    city: string;
	    country: string;
	    postalCode: string;
	  }
	   
	  interface AddressWithUnit extends BasicAddress {
	    unit: string;
	  }
	  ```
	- 接口也可以[[#green]]==继承多个类型：==
	- ```
	  interface Colorful {
	    color: string;
	  }
	   
	  interface Circle {
	    radius: number;
	  }
	   
	  interface ColorfulCircle extends Colorful, Circle {}
	   
	  const cc: ColorfulCircle = {
	    color: "red",
	    radius: 42,
	  };
	  ```
- ## 交叉类型（Intersection Types）
	- TypeScript 也提供了名为交叉类型（Intersection types）的方法，[[#blue]]==用于合并已经存在的对象类型==。
	- 交叉类型的定义需要用到 `&` 操作符：
	- ```
	  interface Colorful {
	    color: string;
	  }
	  interface Circle {
	    radius: number;
	  }
	   
	  type ColorfulCircle = Colorful & Circle;
	  ```
- ## 接口继承与交叉类型（Interfaces vs Intersections）
	- 这两种方式在合并类型上看起来很相似，但实际上还是有很大的不同。[[#green]]==最原则性的不同就是在于冲突怎么处理==，这也是你决定选择那种方式的主要原因。
	- ```
	  interface Colorful {
	    color: string;
	  }
	  
	  interface ColorfulSub extends Colorful {
	    color: number
	  }
	  
	  // Interface 'ColorfulSub' incorrectly extends interface 'Colorful'.
	  // Types of property 'color' are incompatible.
	  // Type 'number' is not assignable to type 'string'.
	  
	  ```
	- 使用继承的方式，如果[[#red]]==重写类型会导致编译错误==，但交叉类型不会：
	- ```
	  interface Colorful {
	    color: string;
	  }
	  
	  type ColorfulSub = Colorful & {
	    color: number
	  }
	  ```
	- > 虽然不会报错，那 `color` 属性的类型是什么呢，答案是 `never`，取得是 `string` 和 `number` 的交集。
- ## 泛型对象类型（Generic Object Types）
	- 我们创建一个泛型 `Box` ，它声明了一个类型参数 (type parameter)：
	- ```
	  interface Box<Type> {
	    contents: Type;
	  }
	  ```
	- > 你可以这样理解：`Box` 的 `Type` 就是 `contents` 拥有的类型 `Type`。
	- 当我们引用 `Box` 的时候，我们需要给予一个类型实参替换掉 `Type`：
	- ```
	  let box: Box<string>;
	  ```
	- 把 `Box` 想象成一个实际类型的模板，`Type` 就是一个占位符，可以被替代为具体的类型。
	- ### `Array`   类型（The Array Type）
	  background-color:: pink
		- 我们之前讲过 `Array` 类型，当我们这样写类型 `number[]` 或者 `string[]` 的时候，其实它们只是 `Array<number>` 和 `Array<string>` 的简写形式而已。
		- `Array` 本身就是一个泛型：
		- ```
		  interface Array<Type> {
		    /**
		     * Gets or sets the length of the array.
		     */
		    length: number;
		   
		    /**
		     * Removes the last element from an array and returns it.
		     */
		    pop(): Type | undefined;
		   
		    /**
		     * Appends new elements to an array, and returns the new length of the array.
		     */
		    push(...items: Type[]): number;
		   
		    // ...
		  }
		  ```
		- 现代 JavaScript 也提供其他是泛型的数据结构，比如 `Map<K, V>` ， `Set<T>` 和 `Promise<T>`。因为 `Map` 、`Set` 、`Promise`的行为表现，它们可以跟任何类型搭配使用。
	- ### `ReadonlyArray`   类型（The ReadonlyArray Type）
	  background-color:: pink
		- `ReadonlyArray` 是一个特殊类型，它可以描述数组不能被改变。
		- ```
		  function doStuff(values: ReadonlyArray<string>) {
		    // We can read from 'values'...
		    const copy = values.slice();
		    console.log(`The first value is ${values[0]}`);
		   
		    // ...but we can't mutate 'values'.
		    values.push("hello!");
		    // Property 'push' does not exist on type 'readonly string[]'.
		  }
		  ```
		- TypeScript 也针对 `ReadonlyArray<Type>` 提供了更简短的写法 `readonly Type[]`。
		- ```
		  function doStuff(values: readonly string[]) {
		    // We can read from 'values'...
		    const copy = values.slice();
		    console.log(`The first value is ${values[0]}`);
		   
		    // ...but we can't mutate 'values'.
		    values.push("hello!");
		    // Property 'push' does not exist on type 'readonly string[]'.
		  }
		  ```
		- 最后有一点要注意，就是 `Arrays` 和 `ReadonlyArray` 并[[#red]]==不能双向赋值==：
		- ```
		  let x: readonly string[] = [];
		  let y: string[] = [];
		   
		  x = y; // ok
		  y = x; // The type 'readonly string[]' is 'readonly' and cannot be assigned to the mutable type 'string[]'.
		  ```
	- ### 元组类型（Tuple Types）
	  background-color:: pink
		- 元组类型是另外一种 `Array` 类型，当你明确知道数组包含多少个元素，并且每个位置元素的类型都明确知道的时候，就适合使用元组类型。
		- ```
		  type StringNumberPair = [string, number];
		  ```
		- `StringNumberPair` 描述了一个数组，索引 0 的值的类型是 `string`，索引 1 的值的类型是 `number`。
		- > 元组类型在重度依赖约定的 API 中很有用，因为它会让每个元素的意义都很明显。当我们解构的时候，元组给了我们命名变量的自由度。在上面的例子中，我们可以命名元素 `0` 和 `1` 为我们想要的名字。
		  >
		  >> 然而，也不是每个用户都这样认为，所以有的时候，使用一个带有描述属性名字的对象也许是个更好的方式。
		- 除了长度检查，简单的元组类型跟声明了 `length` 属性和具体的索引属性的 `Array` 是一样的。
		- ```
		  interface StringNumberPair {
		    // specialized properties
		    length: 2;
		    0: string;
		    1: number;
		   
		    // Other 'Array<string | number>' members...
		    slice(start?: number, end?: number): Array<string | number>;
		  }
		  ```
		- 在元组类型中，你也可以写一个**可选属性**，但可选元素必须在最后面，而且也会影响类型的 `length` 。
		- ```
		  type Either2dOr3d = [number, number, number?];
		   
		  function setCoordinate(coord: Either2dOr3d) {
		    const [x, y, z] = coord;         
		    // const z: number | undefined
		   
		    console.log(`Provided coordinates had ${coord.length} dimensions`);
		    // (property) length: 2 | 3
		  }
		  ```
		- Tuples 也可以使用剩余元素语法，但必须是 array/tuple 类型：
		- ```
		  type StringNumberBooleans = [string, number, ...boolean[]];
		  type StringBooleansNumber = [string, ...boolean[], number];
		  type BooleansStringNumber = [...boolean[], string, number];
		  ```
		-