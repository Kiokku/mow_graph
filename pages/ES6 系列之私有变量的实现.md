- > https://github.com/mqyqingfeng/Blog/issues/110
-
- ## 1. 约定
	- ### 实现
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
	-