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
	-
	-