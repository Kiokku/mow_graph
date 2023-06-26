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
		-
	-