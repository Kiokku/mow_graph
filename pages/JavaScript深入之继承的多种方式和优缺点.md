- > https://github.com/mqyqingfeng/Blog/issues/16
- > 《JavaScript高级程序设计》
-
- ## 1.原型链继承
	- ```
	  function Parent () {
	      this.name = 'kevin';
	  }
	  
	  Parent.prototype.getName = function () {
	      console.log(this.name);
	  }
	  
	  function Child () {
	  
	  }
	  
	  Child.prototype = new Parent();
	  
	  var child1 = new Child();
	  
	  console.log(child1.getName()) // kevin
	  ```
	- 问题：
		- 1.引用类型的属性被[[#blue]]==所有实例共享==，举个例子：
		- ```
		  function Parent () {
		      this.names = ['kevin', 'daisy'];
		  }
		  
		  function Child () {
		  
		  }
		  
		  Child.prototype = new Parent();
		  
		  var child1 = new Child();
		  
		  child1.names.push('yayu');
		  
		  console.log(child1.names); // ["kevin", "daisy", "yayu"]
		  
		  var child2 = new Child();
		  
		  console.log(child2.names); // ["kevin", "daisy", "yayu"]
		  ```
		- 2.在创建 Child 的实例时，不能向Parent传参
- ## 2.借用构造函数(经典继承)
	- ```
	  function Parent () {
	      this.names = ['kevin', 'daisy'];
	  }
	  
	  function Child () {
	      Parent.call(this);
	  }
	  
	  var child1 = new Child();
	  
	  child1.names.push('yayu');
	  
	  console.log(child1.names); // ["kevin", "daisy", "yayu"]
	  
	  var child2 = new Child();
	  
	  console.log(child2.names); // ["kevin", "daisy"]
	  ```
	- [[#green]]==优点==：
		- 1.避免了引用类型的属性被所有实例共享
		- 2.可以在 Child 中向 Parent 传参
		- 举个例子：
			- ```
			  function Parent (name) {
			      this.name = name;
			  }
			  
			  function Child (name) {
			      Parent.call(this, name);
			  }
			  
			  var child1 = new Child('kevin');
			  
			  console.log(child1.name); // kevin
			  
			  var child2 = new Child('daisy');
			  
			  console.log(child2.name); // daisy
			  ```
	- [[#red]]==缺点==：
		- 方法都在构造函数中定义，每次创建实例都会创建一遍方法。
- ## 3.组合继承
	- ```
	  function Parent (name) {
	      this.name = name;
	      this.colors = ['red', 'blue', 'green'];
	  }
	  
	  Parent.prototype.getName = function () {
	      console.log(this.name)
	  }
	  
	  function Child (name, age) {
	  
	      Parent.call(this, name);
	      
	      this.age = age;
	  
	  }
	  
	  Child.prototype = new Parent();
	  Child.prototype.constructor = Child;
	  
	  var child1 = new Child('kevin', '18');
	  
	  child1.colors.push('black');
	  
	  console.log(child1.name); // kevin
	  console.log(child1.age); // 18
	  console.log(child1.colors); // ["red", "blue", "green", "black"]
	  
	  var child2 = new Child('daisy', '20');
	  
	  console.log(child2.name); // daisy
	  console.log(child2.age); // 20
	  console.log(child2.colors); // ["red", "blue", "green"]
	  ```
	-
-