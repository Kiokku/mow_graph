- >https://github.com/mqyqingfeng/Blog/issues/44
-
- > 📍 需求
  >
  >我们现在需要写一个 foo 函数，这个函数返回首次调用时的 Date 对象，注意是**首次**。
- ## 解决一：普通方法
	- ```
	  var t;
	  function foo() {
	      if (t) return t;
	      t = new Date()
	      return t;
	  }
	  ```
	- 问题有两个，一是[[#red]]==污染了全局变量==，二是每次调用 foo 的时候都需要进行一次判断。
- ## 解决二：闭包
	- 我们很容易想到用闭包避免污染全局变量。
	- ```
	  var foo = (function() {
	      var t;
	      return function() {
	          if (t) return t;
	          t = new Date();
	          return t;
	      }
	  })();
	  
	  // foo为IIFE，产生闭包。
	  ```
	- 然而还是[[#red]]==没有解决调用时都必须进行一次判断的问题==。
- ## 解决三：函数对象
	- 函数也是一种对象，利用这个特性，我们也可以解决这个问题。
	- ```
	  function foo() {
	      if (foo.t) return foo.t;
	      foo.t = new Date();
	      return foo.t;
	  }
	  ```
	- 依旧[[#red]]==没有解决调用时都必须进行一次判断的问题==。
- ## 解决四：惰性函数
	-