- > https://github.com/mqyqingfeng/Blog/issues/46
-
- ## 定义
	- **函数记忆**是指将上次的计算结果缓存起来，当下次调用时，如果遇到相同的参数，就直接返回缓存中的数据。
	- ```
	  function add(a, b) {
	      return a + b;
	  }
	  
	  // 假设 memoize 可以实现函数记忆
	  var memoizedAdd = memoize(add);
	  
	  memoizedAdd(1, 2) // 3
	  memoizedAdd(1, 2) // 相同的参数，第二次调用时，从缓存中取出数据，而非重新计算一次
	  ```
- ## 原理
	- 实现这样一个 memoize 函数很简单，原理上只用把参数和对应的结果数据存到一个对象中，调用时，判断参数对应的数据是否存在，存在就返回对应的结果数据。
- ## 第一版
	- ```
	  // 第一版 (来自《JavaScript权威指南》)
	  function memoize(f) {
	      var cache = {};
	      return function(){
	          var key = arguments.length + Array.prototype.join.call(arguments, ",");
	          if (key in cache) {
	              return cache[key]
	          }
	          else {
	              return cache[key] = f.apply(this, arguments)
	          }
	      }
	  }
	  ```