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
		- 1.引用类型的属性被所有实例共享，举个例子：
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
		-