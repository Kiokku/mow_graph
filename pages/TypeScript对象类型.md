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
	-
		-
	-