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
	  	// fn之外的参数
	      var args = [].slice.call(arguments, 1);
	      // 返回 记住之前传入参数的闭包
	      return function() {
	          return fn.apply(this, args.concat([].slice.call(arguments)));
	      };
	  }
	  
	  function curry(fn, length) {
	  	// fn.length的值为函数形参的数量；
	      length = length || fn.length;
	  
	      var slice = Array.prototype.slice;
	      
	  	// curry的作用，返回闭包
	      return function() {
	      	// 不满足出口条件，返回闭包
	          if (arguments.length < length) {
	              var combined = [fn].concat(slice.call(arguments));
	              // 调用sub_curry是希望得到一个能缓存argument的函数，还要改一下出口条件，只要传入length - arguments.length 个参数，就能满足出口条件
	              return curry(sub_curry.apply(this, combined), length - arguments.length);
	          } else {
	              // 满足出口条件后，返回fn执行结果
	              return fn.apply(this, arguments);
	          }
	      };
	  }
	  
	  // 明确curry函数的作用：
	  // 1、返回闭包函数
	  // 2、确定进入出口的条件为：argument.length >= fn.length
	  // 3、执行闭包的最终出口是执行fn函数
	  // 4、在没到出口时，调用curry 返回闭包（参数传入辅助函数调用的结果和下一闭包的出口条件）
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
	- 回到真正的 curry 函数，我们以下面的例子为例：
	- ```
	  var fn0 = function(a, b, c, d) {
	      return [a, b, c, d];
	  }
	  
	  var fn1 = curry(fn0);
	  
	  fn1("a", "b")("c")("d")
	  ```
	- 当执行 fn1("a", "b") 时：
	  background-color:: blue
	- ```
	  fn1("a", "b")
	  // 相当于
	  curry(fn0)("a", "b")
	  // 相当于
	  curry(sub_curry(fn0, "a", "b"))
	  // 相当于
	  // 注意 ... 只是一个示意，表示该函数执行时传入的参数会作为 fn0 后面的参数传入
	  curry(function(...){
	      return fn0("a", "b", ...)
	  })
	  ```
	- 当执行 fn1("a", "b")("c") 时，函数返回：
	  background-color:: blue
	- ```
	  curry(sub_curry(function(...){
	      return fn0("a", "b", ...)
	  }), "c")
	  // 相当于
	  curry(function(...){
	      return (function(...) {return fn0("a", "b", ...)})("c")
	  })
	  // 相当于
	  curry(function(...){
	       return fn0("a", "b", "c", ...)
	  })
	  ```
	- 当执行 fn1("a", "b")("c")("d") 时，此时 arguments.length < length 为 false ，执行 fn(arguments)，相当于：
	  background-color:: blue
	- ```
	  (function(...){
	      return fn0("a", "b", "c", ...)
	  })("d")
	  // 相当于
	  fn0("a", "b", "c", "d")
	  ```
	- > 所以，其实整段代码又很好理解：sub_curry 的作用就是用函数包裹原函数，然后给原函数传入之前的参数，当执行 fn0(...)(...) 的时候，执行包裹函数，返回原函数，然后再调用 sub_curry 再包裹原函数，然后将新的参数混合旧的参数再传入原函数，直到函数参数的数目达到要求为止。
- ## 更易懂的实现
	- id:: 6499b665-8c15-47cb-9c47-99390f3239b0
	  ```
	  function curry(fn, args) {
	      var length = fn.length;
	  
	      args = args || [];
	  
	      return function() {
	  
	          var _args = args.slice(0),
	  
	              arg, i;
	  
	          for (i = 0; i < arguments.length; i++) {
	  
	              arg = arguments[i];
	  
	              _args.push(arg);
	  
	          }
	          if (_args.length < length) {
	              return curry.call(this, fn, _args);
	          }
	          else {
	              return fn.apply(this, _args);
	          }
	      }
	  }
	  
	  
	  var fn = curry(function(a, b, c) {
	      console.log([a, b, c]);
	  });
	  
	  fn("a", "b", "c") // ["a", "b", "c"]
	  fn("a", "b")("c") // ["a", "b", "c"]
	  fn("a")("b")("c") // ["a", "b", "c"]
	  fn("a")("b", "c") // ["a", "b", "c"]
	  ```
- ## 第三版
	- curry 函数写到这里其实已经很完善了，但是注意这个函数的传参顺序必须是从左到右，根据形参的顺序依次传入，如果我不想根据这个顺序传呢？
	- 我们可以创建一个**占位符**，比如这样：
	- ```
	  var fn = curry(function(a, b, c) {
	      console.log([a, b, c]);
	  });
	  
	  fn("a", _, "c")("b") // ["a", "b", "c"]
	  ```
	- ```
	  // 第三版
	  function curry(fn, args, holes) {
	      length = fn.length;
	  
	      args = args || [];
	  
	      holes = holes || [];
	  
	      return function() {
	  
	          var _args = args.slice(0),
	              _holes = holes.slice(0),
	              argsLen = args.length,
	              holesLen = holes.length,
	              arg, i, index = 0;
	  
	          for (i = 0; i < arguments.length; i++) {
	              arg = arguments[i];
	              // 处理类似 fn(1, _, _, 4)(_, 3) 这种情况，index 需要指向 holes 正确的下标
	              if (arg === _ && holesLen) {
	                  index++
	                  if (index > holesLen) {
	                      _args.push(arg);
	                      _holes.push(argsLen - 1 + index - holesLen)
	                  }
	              }
	              // 处理类似 fn(1)(_) 这种情况
	              else if (arg === _) {
	                  _args.push(arg);
	                  _holes.push(argsLen + i);
	              }
	              // 处理类似 fn(_, 2)(1) 这种情况
	              else if (holesLen) {
	                  // fn(_, 2)(_, 3)
	                  if (index >= holesLen) {
	                      _args.push(arg);
	                  }
	                  // fn(_, 2)(1) 用参数 1 替换占位符
	                  else {
	                      _args.splice(_holes[index], 1, arg);
	                      _holes.splice(index, 1)
	                  }
	              }
	              else {
	                  _args.push(arg);
	              }
	  
	          }
	          if (_holes.length || _args.length < length) {
	              return curry.call(this, fn, _args, _holes);
	          }
	          else {
	              return fn.apply(this, _args);
	          }
	      }
	  }
	  
	  var _ = {};
	  
	  var fn = curry(function(a, b, c, d, e) {
	      console.log([a, b, c, d, e]);
	  });
	  
	  // 验证 输出全部都是 [1, 2, 3, 4, 5]
	  fn(1, 2, 3, 4, 5);
	  fn(_, 2, 3, 4, 5)(1);
	  fn(1, _, 3, 4, 5)(2);
	  fn(1, _, 3)(_, 4)(2)(5);
	  fn(1, _, _, 4)(_, 3)(2)(5);
	  fn(_, 2)(_, _, 4)(1)(3)(5)
	  ```
	-