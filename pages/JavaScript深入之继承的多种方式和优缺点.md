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
	-