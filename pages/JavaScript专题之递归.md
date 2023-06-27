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
- ## 执行上下文栈
	- 在[[JavaScript深入之执行上下文栈]]中，我们知道：
	- 当执行一个函数的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。
	- 试着对阶乘函数分析执行的过程，我们会发现，JavaScript 会不停的创建执行上下文压入执行上下文栈，对于内存而言，维护这么多的执行上下文也是一笔不小的开销呐！那么，我们该如何优化呢？
	- 答案就是**尾调用**。
- ## 尾调用
	- 尾调用，是指函数内部的最后一个动作是函数调用。该调用的返回值，直接返回给函数。
	- ```
	  // 尾调用
	  function f(x){
	      return g(x);
	  }
	  
	  // 非尾调用
	  function f(x){
	      return g(x) + 1;
	  }
	  ```
	- 两者又有什么区别呢？答案就是[[#red]]==执行上下文栈的变化不一样==。
	- 我们模拟下第一个**尾调用**函数执行时的执行上下文栈变化：
	- ```
	  // 伪代码
	  ECStack.push(<f> functionContext);
	  
	  ECStack.pop();
	  
	  ECStack.push(<g> functionContext);
	  
	  ECStack.pop();
	  ```
	- 我们再来模拟一下第二个**非尾调用**函数执行时的执行上下文栈变化：
	- ```
	  ECStack.push(<f> functionContext);
	  
	  ECStack.push(<g> functionContext);
	  
	  ECStack.pop();
	  
	  ECStack.pop();
	  ```
	- 也就说尾调用函数执行时，虽然也调用了一个函数，但是因为原来的的函数执行完毕，执行上下文会被弹出，执行上下文栈中相当于只多压入了一个执行上下文。然而非尾调用函数，就会创建多个执行上下文压入执行上下文栈。
	- 函数调用自身，称为递归。如果[[#blue]]==尾调用自身，就称为尾递归==。
- ## 阶乘函数优化
	-