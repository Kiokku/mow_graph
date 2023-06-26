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
	-