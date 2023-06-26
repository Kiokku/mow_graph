- > https://github.com/mqyqingfeng/Blog/issues/42
-
- ## 定义
	- > 在数学和计算机科学中，柯里化是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术。
	- ```
	  function add(a, b) {
	      return a + b;
	  }
	  
	  // 执行 add 函数，一次传入两个参数即可
	  add(1, 2) // 3
	  
	  // 假设有一个 curry 函数可以做到柯里化
	  var addCurry = curry(add);
	  addCurry(1)(2) // 3
	  ```
- ## 用途
	- **参数复用**。本质上是降低通用性，提高适用性。
	- 如果我们仅仅是把参数一个一个传进去，意义可能不大，但是如果我们是把柯里化后的函数传给其他函数比如 map 呢？
	- 比如我们有这样一段数据：
	- ```
	  var person = [{name: 'kevin'}, {name: 'daisy'}]
	  ```
	- 如果我们要获取所有的 name 值，我们可以这样做：
	- ```
	  var name = person.map(function (item) {
	      return item.name;
	  })
	  ```
	- 不过如果我们有 curry 函数：
	- ```
	  var prop = curry(function (key, obj) {
	      return obj[key]
	  });
	  
	  var name = person.map(prop('name'))
	  ```
	- `person.map(prop('name'))` 就好像直白的告诉你：person 对象遍历(map)获取(prop) name 属性。
- ## 第一版
	- ```
	  // 第一版
	  var curry = function (fn) {
	      var args = [].slice.call(arguments, 1);
	      return function() {
	          var newArgs = args.concat([].slice.call(arguments));
	          return fn.apply(this, newArgs);
	      };
	  };
	  ```
	- 已经有柯里化的感觉了，但是还没有达到要求，不过我们可以把这个函数用作辅助函数，帮助我们写真正的 curry 函数。
- ## 第二版
	- ```
	  // 第二版
	  function sub_curry(fn) {
	      var args = [].slice.call(arguments, 1);
	      return function() {
	          return fn.apply(this, args.concat([].slice.call(arguments)));
	      };
	  }
	  
	  function curry(fn, length) {
	  
	      length = length || fn.length;
	  
	      var slice = Array.prototype.slice;
	  
	      return function() {
	          if (arguments.length < length) {
	              var combined = [fn].concat(slice.call(arguments));
	              return curry(sub_curry.apply(this, combined), length - arguments.length);
	          } else {
	              return fn.apply(this, arguments);
	          }
	      };
	  }
	  ```
	- 为了让大家更好的理解这个 curry 函数，我给大家写个极简版的代码：
	- ```
	  function sub_curry(fn){
	      return function(){
	          return fn()
	      }
	  }
	  
	  function curry(fn, length){
	      length = length || 4;
	      return function(){
	          if (length > 1) {
	              return curry(sub_curry(fn), --length)
	          }
	          else {
	              return fn()
	          }
	      }
	  }
	  
	  var fn0 = function(){
	      console.log(1)
	  }
	  
	  var fn1 = curry(fn0)
	  
	  fn1()()()() // 1
	  ```
	-
	-