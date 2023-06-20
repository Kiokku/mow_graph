- > https://github.com/mqyqingfeng/Blog/issues/41
-
- ## 相等
	- `underscore`中的`eq`函数：
		- 1. NaN 和 NaN 是相等
		  2. [1] 和 [1] 是相等
		  3. {value: 1} 和 {value: 1} 是相等
		  4. 1 和 new Number(1) 是相等
		  5. 'Curly' 和 new String('Curly') 是相等
		  6. true 和 new Boolean(true) 是相等
- ## 目标
	- 我们的目标是写一个 eq 函数用来判断两个参数是否相等，使用效果如下：
	- ```
	  function eq(a, b) { ... }
	  
	  var a = [1];
	  var b = [1];
	  console.log(eq(a, b)) // true
	  ```
- ## +0 与 -0
	- 如果 a === b 的结果为 true， 那么 a 和 b 就是相等的吗？一般情况下，当然是这样的，但是有一个特殊的例子，就是 +0 和 -0。
	- ```
	  // 表现1
	  console.log(+0 === -0); // true
	  
	  // 表现2
	  (-0).toString() // '0'
	  (+0).toString() // '0'
	  
	  // 表现3
	  -0 < +0 // false
	  +0 < -0 // false
	  ```
	- [[#blue]]==即便如此，两者依然是不同的：==
	- ```
	  1 / +0 // Infinity
	  1 / -0 // -Infinity
	  
	  1 / +0 === 1 / -0 // false
	  
	  Math.round(-0.1) // -0
	  ```
	- 那么我们又该如何在 === 结果为 true 的时候，区别 0 和 -0 得出正确的结果呢？我们可以这样做：
	- ```
	  function eq(a, b){
	  	// Infinity和-Infinity
	      if (a === b) return a !== 0 || 1 / a === 1 / b;
	      return false;
	  }
	  
	  console.log(eq(0, 0)) // true
	  console.log(eq(0, -0)) // false
	  ```
- ## NaN
	- `console.log(NaN === NaN); // false`
	- 利用 NaN 不等于自身的特性，我们可以区别出 NaN：`if (a !== a) return b !== b;`
- ## eq 函数
	- ```
	  // eq 第一版
	  // 用来过滤掉简单的类型比较，复杂的对象使用 deepEq 函数进行处理
	  function eq(a, b) {
	  
	      // === 结果为 true 的区别出 +0 和 -0
	      if (a === b) return a !== 0 || 1 / a === 1 / b;
	  
	      // typeof null 的结果为 object ，这里做判断，是为了让有 null 的情况尽早退出函数
	      if (a == null || b == null) return false;
	  
	      // 判断 NaN
	      if (a !== a) return b !== b;
	  
	      // 判断参数 a 类型，如果是基本类型，在这里可以直接返回 false
	      var type = typeof a;
	      if (type !== 'function' && type !== 'object' && typeof b != 'object') return false;
	  
	      // 更复杂的对象使用 deepEq 函数进行深度比较
	      return deepEq(a, b);
	  };
	  ```
-