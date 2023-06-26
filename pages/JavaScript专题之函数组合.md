- >https://github.com/mqyqingfeng/Blog/issues/45
-
- > 🌟 需求
  >
  > 我们需要写一个函数，输入 'kevin'，返回 'HELLO, KEVIN'。
-
- ## 尝试
	- ```
	  var toUpperCase = function(x) { return x.toUpperCase(); };
	  var hello = function(x) { return 'HELLO, ' + x; };
	  
	  var greet = function(x){
	      return hello(toUpperCase(x));
	  };
	  
	  greet('kevin');
	  ```
	- 还好我们只有两个步骤，首先小写转大写，然后拼接字符串。如果有更多的操作，greet 函数里就需要更多的嵌套，类似于 `fn3(fn2(fn1(fn0(x))))`。
- ## 优化
	- 试想我们写个 compose 函数：
	- ```
	  ```