- > https://github.com/mqyqingfeng/Blog/issues/109
-
- ## Decorator
	- 装饰器主要用于:
		- 1. 装饰类
		- 2. 装饰方法或属性
	- ### 装饰类
	  background-color:: pink
		- ```
		  @annotation
		  class MyClass { }
		  
		  function annotation(target) {
		     target.annotated = true;
		  }
		  ```
	- ### 装饰方法或属性
	  background-color:: pink
		- ```
		  class MyClass {
		    @readonly
		    method() { }
		  }
		  
		  function readonly(target, name, descriptor) {
		    descriptor.writable = false;
		    return descriptor;
		  }
		  ```
- ## Babel
	-