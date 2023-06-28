- > https://ts.yayujs.com/handbook/IndexedAccessTypes.html
-
- ## 索引访问类型（Indexed Access Types）
	- 我们可以使用 **索引访问类型（indexed access type）** 查找另外一个类型上的特定属性：
	- ```
	  type Person = { age: number; name: string; alive: boolean };
	  type Age = Person["age"];
	  // type Age = number
	  ```
	- 因为索引名本身就是一个类型，所以我们也可以使用联合、`keyof` 或者其他类型：
	- ```
	  type I1 = Person["age" | "name"];  
	  // type I1 = string | number
	   
	  type I2 = Person[keyof Person];
	  // type I2 = string | number | boolean
	   
	  type AliveOrName = "alive" | "name";
	  type I3 = Person[AliveOrName];  
	  // type I3 = string | boolean
	  ```
	- 如果你尝试查找一个不存在的属性，TypeScript 会报错：
	- ```
	  type I1 = Person["alve"];
	  // Property 'alve' does not exist on type 'Person'.
	  ```
	- 接下来是另外一个示例，我们使用 `number` 来获取数组元素的类型。结合 `typeof` 可以方便的捕获数组字面量的元素类型：
	- ```
	  const MyArray = [
	    { name: "Alice", age: 15 },
	    { name: "Bob", age: 23 },
	    { name: "Eve", age: 38 },
	  ];
	   
	  type Person = typeof MyArray[number];
	         
	  // type Person = {
	  //    name: string;
	  //    age: number;
	  // }
	  
	  type Age = typeof MyArray[number]["age"];  
	  // type Age = number
	  
	  // Or
	  type Age2 = Person["age"];   
	  // type Age2 = number
	  ```
	- 作为索引的只能是类型，这意味着你不能使用 `const` 创建一个变量引用：
	- ```
	  const key = "age";
	  type Age = Person[key];
	  
	  // Type 'key' cannot be used as an index type.
	  // 'key' refers to a value, but is being used as a type here. Did you mean 'typeof key'?
	  ```
	- 然而你可以使用类型别名实现类似的重构：
	- ```
	  type key = "age";
	  type Age = Person[key];
	  ```
	-