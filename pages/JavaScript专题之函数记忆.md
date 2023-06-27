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
	- 我们来测试一下：
	- ```
	  var add = function(a, b, c) {
	    return a + b + c
	  }
	  
	  var memoizedAdd = memoize(add)
	  
	  console.time('use memoize')
	  for(var i = 0; i < 100000; i++) {
	      memoizedAdd(1, 2, 3)
	  }
	  console.timeEnd('use memoize')
	  
	  console.time('not use memoize')
	  for(var i = 0; i < 100000; i++) {
	      add(1, 2, 3)
	  }
	  console.timeEnd('not use memoize')
	  ```
	- [[#blue]]==在 Chrome 中，使用 memoize 大约耗时 60ms，如果我们不使用函数记忆，大约耗时 1.3 ms 左右。==
	- > ⚠️ 注意
	  >
	  >我们使用了看似高大上的函数记忆，结果却更加耗时，这个例子近乎有 60 倍呢！
	  >需要注意的是，函数记忆只是一种编程技巧，[[#green]]==本质上是牺牲算法的空间复杂度以换取更优的时间复杂度==，在客户端 JavaScript 中代码的执行时间复杂度往往成为瓶颈，因此在大多数场景下，这种牺牲空间换取时间的做法以提升程序执行效率的做法是非常可取的。
- ## 第二版
	- 因为第一版使用了 join 方法，我们很容易想到当参数是对象的时候，就会自动调用 toString 方法转换成 `[Object object]`，再拼接字符串作为 key 值。我们写个 demo 验证一下这个问题：
	- ```
	  var propValue = function(obj){
	      return obj.value
	  }
	  
	  var memoizedAdd = memoize(propValue)
	  
	  console.log(memoizedAdd({value: 1})) // 1
	  console.log(memoizedAdd({value: 2})) // 1
	  ```
	- 两者都返回了 1，显然是有问题的，所以我们看看 underscore 的 memoize 函数是如何实现的：
	- ```
	  // 第二版 (来自 underscore 的实现)
	  var memoize = function(func, hasher) {
	      var memoize = function(key) {
	          var cache = memoize.cache;
	          var address = '' + (hasher ? hasher.apply(this, arguments) : key);
	          if (!cache[address]) {
	              cache[address] = func.apply(this, arguments);
	          }
	          return cache[address];
	      };
	      memoize.cache = {};
	      return memoize;
	  };
	  ```
	- 如果要支持多参数，我们就需要传入 hasher 函数，自定义存储的 key 值。所以我们考虑使用 JSON.stringify：
	- ```
	  var add = function(a, b, c) {
	    return a + b + c
	  }
	  
	  var memoizedAdd = memoize(add, function(){
	      var args = Array.prototype.slice.call(arguments)
	      return JSON.stringify(args)
	  })
	  
	  console.log(memoizedAdd(1, 2, 3)) // 6
	  console.log(memoizedAdd(1, 2, 4)) // 7
	  ```
	- 如果使用 JSON.stringify，参数是对象的问题也可以得到解决，因为存储的是对象序列化后的字符串。
- ## 适用场景
	- 我们以斐波那契数列为例：
	- ```
	  var count = 0;
	  var fibonacci = function(n){
	      count++;
	      return n < 2? n : fibonacci(n-1) + fibonacci(n-2);
	  };
	  for (var i = 0; i <= 10; i++){
	      fibonacci(i)
	  }
	  
	  console.log(count) // 453
	  ```
	- 我们会发现最后的 count 数为 453，也就是说 fibonacci 函数被调用了 453 次！
	- 使用函数记忆呢？
	- ```
	  var count = 0;
	  var fibonacci = function(n) {
	      count++;
	      return n < 2 ? n : fibonacci(n - 1) + fibonacci(n - 2);
	  };
	  
	  fibonacci = memoize(fibonacci)
	  
	  for (var i = 0; i <= 10; i++) {
	      fibonacci(i)
	  }
	  
	  console.log(count) // 12
	  ```
	- 我们会发现最后的总次数为 12 次，因为使用了函数记忆，调用次数从 453 次降低为了 12 次!
	-