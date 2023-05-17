- > [https://github.com/mqyqingfeng/Blog/issues/10](https://github.com/mqyqingfeng/Blog/issues/10)
-
- ## 定义
	- 在《JavaScript高级程序设计》第三版 4.1.3，讲到传递参数：
	- > ECMAScript中所有函数的参数都是[[#blue]]==按值传递==的。
	- > 把函数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样。
- ## 按值传递
	- 举个简单的例子：
	- ```
	  var value = 1;
	  function foo(v) {
	      v = 2;
	      console.log(v); //2
	  }
	  foo(value);
	  console.log(value) // 1
	  ```
	- 很好理解，当传递 value 到函数 foo 中，相当于拷贝了一份 value，假设拷贝的这份叫 _value，函数中修改的都是 _value 的值，而不会影响原来的 value 值。
- ## 引用传递？
	- 拷贝虽然很好理解，但是当值是一个复杂的数据结构的时候，拷贝就会产生性能上的问题。
	- 所以还有另一种传递方式叫做[[#blue]]==按引用传递==。
	- 所谓按引用传递，就是传递对象的引用(内存地址)，函数内部对参数的任何改变都会影响该对象的值，因为两者引用的是同一个对象。
	- 举个例子：
	- ```
	  var obj = {
	      value: 1
	  };
	  function foo(o) {
	      o.value = 2;
	      console.log(o.value); //2
	  }
	  foo(obj);
	  console.log(obj.value) // 2
	  ```
	-