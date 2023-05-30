- > https://github.com/mqyqingfeng/Blog/issues/15
- > 《JavaScript高级程序设计》
-
- ## 1. 工厂模式
	- ```
	  function createPerson(name) {
	      var o = new Object();
	      o.name = name;
	      o.getName = function () {
	          console.log(this.name);
	      };
	  
	      return o;
	  }
	  
	  var person1 = createPerson('kevin');
	  ```
	- [[#red]]==缺点==：对象无法识别，因为所有的实例都指向一个原型
- ## 2. 构造函数模式
	- ```
	  function Person(name) {
	      this.name = name;
	      this.getName = function () {
	          console.log(this.name);
	      };
	  }
	  
	  var person1 = new Person('kevin');
	  ```
	- [[#green]]==优点==：实例可以识别为一个特定的类型
	- [[#red]]==缺点==：每次创建实例时，每个方法都要被创建一次
	- [[#blue]]==构造函数模式优化==
		- ```
		  function Person(name) {
		      this.name = name;
		      this.getName = getName;
		  }
		  
		  function getName() {
		      console.log(this.name);
		  }
		  
		  var person1 = new Person('kevin');
		  ```
		- [[#green]]==优点==：解决了每个方法都要被重新创建的问题
		- [[#red]]==缺点==：这叫啥封装……
- ## 3. 原型模式
	- ```
	  function Person(name) {
	  
	  }
	  
	  Person.prototype.name = 'keivn';
	  Person.prototype.getName = function () {
	      console.log(this.name);
	  };
	  
	  var person1 = new Person();
	  ```
	-
-