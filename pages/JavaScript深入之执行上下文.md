- > https://github.com/mqyqingfeng/Blog/issues/8
-
- ## 思考题
	- ```
	  var scope = "global scope";
	  function checkscope(){
	      var scope = "local scope";
	      function f(){
	          return scope;
	      }
	      return f();
	  }
	  checkscope();
	  ```
	- ```
	  var scope = "global scope";
	  function checkscope(){
	      var scope = "local scope";
	      function f(){
	          return scope;
	      }
	      return f;
	  }
	  checkscope()();
	  ```
	- 两段代码都会打印'local scope'。虽然两段代码执行的结果一样，但是两段代码究竟有哪些不同呢？
- ## 具体执行分析
	- 我们分析第一段代码：
	- ```
	  var scope = "global scope";
	  function checkscope(){
	      var scope = "local scope";
	      function f(){
	          return scope;
	      }
	      return f();
	  }
	  checkscope();
	  ```
		- 1.执行全局代码，创建全局执行上下文，全局上下文被压入执行上下文栈
		-