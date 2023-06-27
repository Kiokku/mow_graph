- > https://github.com/mqyqingfeng/Blog/issues/49
-
- ## 阶乘
	- ```
	  function factorial(n) {
	      if (n == 1) return n;
	      return n * factorial(n - 1)
	  }
	  
	  console.log(factorial(5)) // 5 * 4 * 3 * 2 * 1 = 120
	  ```
- ## 斐波那契数列
	- ```
	  function fibonacci(n){
	      return n < 2 ? n : fibonacci(n - 1) + fibonacci(n - 2);
	  }
	  
	  console.log(fibonacci(5)) // 1 1 2 3 5
	  ```
- ## 递归条件
	- 构成递归需具备**边界条件**、**递归前进段**和**递归返回段**，当边界条件不满足时，递归前进，当边界条件满足时，递归返回。阶乘中的 `n == 1` 和 斐波那契数列中的 `n < 2` 都是边界条件。
	- 总结一下递归的特点：
		- 子问题须与原始问题为同样的事，且更为简单；
		- 不能无限制地调用本身，须有个出口，化简为非递归状况处理。
	-