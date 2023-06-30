- > https://github.com/mqyqingfeng/Blog/issues/110
-
- ## 1. 约定
	- ### 实现
	  background-color:: blue
		- ```
		  class Example {
		  	constructor() {
		  		this._private = 'private';
		  	}
		  	getName() {
		  		return this._private
		  	}
		  }
		  
		  var ex = new Example();
		  
		  console.log(ex.getName()); // private
		  console.log(ex._private); // private
		  ```
	- ### 优点
	  background-color:: green
		- 1. 写法简单
		  2. 调试方便
		  3. 兼容性好
	- ### 缺点
	  background-color:: red
		- 1. 外部可以访问和修改
		  2. 语言没有配合的机制，如 for in 语句会将所有属性枚举出来
		  3. 命名冲突
- ## 2. 闭包
	- ### 实现一
	  background-color:: blue
		- ```
		  /**
		   * 实现一
		   */
		  class Example {
		    constructor() {
		      var _private = '';
		      _private = 'private';
		      this.getName = function() {return _private}
		    }
		  }
		  
		  var ex = new Example();
		  
		  console.log(ex.getName()); // private
		  console.log(ex._private); // undefined
		  ```
	- ### 优点
	  background-color:: green
		- 1. 无命名冲突
		  2. 外部无法访问和修改
	- ### 缺点
	  background-color:: red
		- 1. constructor 的逻辑变得复杂。[[#red]]==构造函数应该只做对象初始化==的事情，现在为了实现私有变量，必须包含部分方法的实现，代码组织上略不清晰。
		  2. 方法存在于实例，而非原型上，子类也无法使用 super 调用
		  3. 构建增加一点点开销
	- ### 实现二
	  background-color:: blue
		- ```
		  /**
		   * 实现二
		   */
		  const Example = (function() {
		    var _private = '';
		  
		    class Example {
		      constructor() {
		        _private = 'private';
		      }
		      getName() {
		        return _private;
		      }
		    }
		  
		    return Example;
		  
		  })();
		  
		  var ex = new Example();
		  
		  console.log(ex.getName()); // private
		  console.log(ex._private); // undefined
		  ```
	- ### 优点
	  background-color:: green
		- 1. 无命名冲突
		  2. 外部无法访问和修改
	- ### 缺点
	  background-color:: red
		- 1. 写法有一点复杂
		  2. 构建增加一点点开销
- ## 3. Symbol
	- ### 实现
	  background-color:: blue
		- ```
		  const Example = (function() {
		      var _private = Symbol('private');
		  
		      class Example {
		          constructor() {
		            this[_private] = 'private';
		          }
		          getName() {
		            return this[_private];
		          }
		      }
		  
		      return Example;
		  })();
		  
		  var ex = new Example();
		  
		  console.log(ex.getName()); // private
		  console.log(ex.name); // undefined
		  ```
	- ### 优点
	  background-color:: green
		- 1. 无命名冲突
		  2. 外部无法访问和修改
		  3. 无性能损失
	- ### 缺点
	  background-color:: red
		- 1. 写法稍微复杂
		  2. 兼容性也还好
- ## 4. WeakMap
	- ### 实现
	  background-color:: blue
		- ```
		  /**
		   * 实现一
		   */
		  const _private = new WeakMap();
		  
		  class Example {
		    constructor() {
		      _private.set(this, 'private');
		    }
		    getName() {
		    	return _private.get(this);
		    }
		  }
		  
		  var ex = new Example();
		  
		  console.log(ex.getName()); // private
		  console.log(ex.name); // undefined
		  ```
		-
		-