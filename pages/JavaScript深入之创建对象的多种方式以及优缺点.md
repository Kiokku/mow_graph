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
	- [[#green]]==优点==：方法不会重新创建
	- [[#red]]==缺点==：1. 所有的属性和方法都共享 2. 不能初始化参数
	- [[#blue]]==原型模式优化一==
		- ```
		  function Person(name) {
		  
		  }
		  
		  Person.prototype = {
		      name: 'kevin',
		      getName: function () {
		          console.log(this.name);
		      }
		  };
		  
		  var person1 = new Person();
		  ```
		- [[#green]]==优点==：封装性好了一点
		- [[#red]]==缺点==：重写了原型，丢失了constructor属性
	- [[#blue]]==原型模式优化二==
		- ```
		  function Person(name) {
		  
		  }
		  
		  Person.prototype = {
		      constructor: Person,
		      name: 'kevin',
		      getName: function () {
		          console.log(this.name);
		      }
		  };
		  
		  var person1 = new Person();
		  ```
		- [[#green]]==优点==：实例可以通过constructor属性找到所属构造函数
		- [[#red]]==缺点==：原型模式该有的缺点还是有
- ## 4. 组合模式
	- 构造函数模式与原型模式双剑合璧。
	- ```
	  function Person(name) {
	      this.name = name;
	  }
	  
	  Person.prototype = {
	      constructor: Person,
	      getName: function () {
	          console.log(this.name);
	      }
	  };
	  
	  var person1 = new Person();
	  ```
	- [[#green]]==优点==：该共享的共享，该私有的私有，使用最广泛的方式
	- [[#red]]==缺点==：有的人就是希望全部都写在一起，即更好的封装性
	- [[#blue]]==动态原型模式==
		- ```
		  function Person(name) {
		      this.name = name;
		      if (typeof this.getName != "function") {   // 相当于constructor，只执行一次
		          Person.prototype.getName = function () {
		              console.log(this.name);
		          }
		      }
		  }
		  
		  var person1 = new Person();
		  ```
		- 注意：使用动态原型模式时，不能用对象字面量重写原型
		- 解释下为什么：
		- ```
		  function Person(name) {
		      this.name = name;
		      if (typeof this.getName != "function") {
		          Person.prototype = {
		              constructor: Person,
		              getName: function () {
		                  console.log(this.name);
		              }
		          }
		      }
		  }
		  
		  var person1 = new Person('kevin');
		  var person2 = new Person('daisy');
		  
		  // 报错 并没有该方法
		  person1.getName();
		  
		  // 注释掉上面的代码，这句是可以执行的。
		  person2.getName();
		  ```
		- 为了解释这个问题，假设开始执行`var person1 = new Person('kevin')`。
		- 我们回顾下 new 的实现步骤：
			- 1. 首先新建一个对象
			  2. 然后将对象的原型指向 Person.prototype
			  3. 然后 Person.apply(obj)
			  4. 返回这个对象
		- 注意这个时候，回顾下 apply 的实现步骤，会执行 obj.Person 方法，这个时候就会执行 if 语句里的内容，注意构造函数的 prototype 属性指向了实例的原型，使用字面量方式直接覆盖 Person.prototype，并不会更改实例的原型的值，person1 依然是指向了以前的原型，而不是 Person.prototype。而之前的原型是没有 getName 方法的，所以就报错了！
		-
		- 如果就是想用字面量方式写代码，可以尝试下这种：
		- ```
		  function Person(name) {
		      this.name = name;
		      if (typeof this.getName != "function") {
		          Person.prototype = {
		              constructor: Person,
		              getName: function () {
		                  console.log(this.name);
		              }
		          }
		  
		          return new Person(name);
		      }
		  }
		  
		  var person1 = new Person('kevin');
		  var person2 = new Person('daisy');
		  
		  person1.getName(); // kevin
		  person2.getName();  // daisy
		  ```
- ### 5.1 寄生构造函数模式
	- ```
	  function Person(name) {
	  
	      var o = new Object();
	      o.name = name;
	      o.getName = function () {
	          console.log(this.name);
	      };
	  
	      return o;
	  
	  }
	  
	  var person1 = new Person('kevin');
	  console.log(person1 instanceof Person) // false
	  console.log(person1 instanceof Object)  // true
	  ```
	-
	-
-