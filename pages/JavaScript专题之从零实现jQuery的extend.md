- > https://github.com/mqyqingfeng/Blog/issues/33
-
- ## extend 基本用法
	- [[#blue]]==合并两个或者更多的对象的内容到第一个对象中。==
	- `jQuery.extend( target [, object1 ] [, objectN ] )`
	- 第一个参数 `target`，表示要拓展的目标，称它为目标对象。
	- 后面的参数，都传入对象，内容都会复制到目标对象中，称它们为待复制对象。
	- 当两个对象出现相同字段的时候，后者会覆盖前者，而[[#red]]==不会进行深层次的覆盖==。
- ## extend 第一版
	- ```
	  // 第一版
	  function extend() {
	      var name, options, copy;
	      var length = arguments.length;
	      var i = 1;
	      var target = arguments[0];
	  
	      for (; i < length; i++) {
	          options = arguments[i];
	          if (options != null) {
	              for (name in options) {
	                  copy = options[name];
	                  if (copy !== undefined){
	                      target[name] = copy;
	                  }
	              }
	          }
	      }
	  
	      return target;
	  };
	  ```
- ## extend 深拷贝
	- 那如何进行深层次的复制呢？jQuery v1.1.4 加入了一个新的用法：
	- `jQuery.extend( [deep], target, object1 [, objectN ] )`
	- ```
	  var obj1 = {
	      a: 1,
	      b: { b1: 1, b2: 2 }
	  };
	  
	  var obj2 = {
	      b: { b1: 3, b3: 4 },
	      c: 3
	  };
	  
	  var obj3 = {
	      d: 4
	  }
	  
	  console.log($.extend(true, obj1, obj2, obj3));
	  
	  // {
	  //    a: 1,
	  //    b: { b1: 3, b2: 2, b3: 4 },
	  //    c: 3,
	  //    d: 4
	  // }
	  ```
	- 因为采用了[[#green]]==深拷贝，会遍历到更深的层次进行添加和覆盖==。
- ## extend 第二版
	- 我们来实现深拷贝的功能，值得注意的是：
		- 1. 需要根据第一个参数的类型，确定 target 和要合并的对象的下标起始值。
		  2. 如果是深拷贝，根据 copy 的类型递归 extend。
	- ```
	  // 第二版
	  function extend() {
	      // 默认不进行深拷贝
	      var deep = false;
	      var name, options, src, copy;
	      var length = arguments.length;
	      // 记录要复制的对象的下标
	      var i = 1;
	      // 第一个参数不传布尔值的情况下，target默认是第一个参数
	      var target = arguments[0] || {};
	      // 如果第一个参数是布尔值，第二个参数是才是target
	      if (typeof target == 'boolean') {
	          deep = target;
	          target = arguments[i] || {};
	          i++;
	      }
	      // 如果target不是对象，我们是无法进行复制的，所以设为{}
	      if (typeof target !== 'object') {
	          target = {}
	      }
	  
	      // 循环遍历要复制的对象们
	      for (; i < length; i++) {
	          // 获取当前对象
	          options = arguments[i];
	          // 要求不能为空 避免extend(a,,b)这种情况
	          if (options != null) {
	              for (name in options) {
	                  // 目标属性值
	                  src = target[name];
	                  // 要复制的对象的属性值
	                  copy = options[name];
	  
	                  if (deep && copy && typeof copy == 'object') {
	                      // 递归调用
	                      target[name] = extend(deep, src, copy);
	                  }
	                  else if (copy !== undefined){
	                      target[name] = copy;
	                  }
	              }
	          }
	      }
	  
	      return target;
	  };
	  ```
	- 在实现上，核心的部分还是跟上篇实现的深浅拷贝函数一致，如果要复制的对象的属性值是一个对象，就[[#blue]]==递归调用 extend==。不过 extend 的实现中，多了很多细节上的判断，比如第一个参数是否是布尔值，target 是否是一个对象，不传参数时的默认值等。
- ## target 是函数
	- 在我们的实现中，`typeof target` 必须等于 `object`，我们才会在这个 `target` 基础上进行拓展，然而我们用 `typeof` 判断一个函数时，会返回`function`，也就是说，我们无法在一个函数上进行拓展！
	- 所以在这里我们还要判断是不是函数，这时候我们便可以使用[[JavaScript专题之类型判断(上)]]中写得 ((648a87c2-89ac-4696-8afb-f60f57a568c3)) 函数:
		- ```
		  if (typeof target !== "object" && !isFunction(target)) {
		      target = {};
		  }
		  ```
- ## 类型不一致
	- 递归调用extend时，判断目标属性值跟要复制的对象的属性值类型是否一致:
		- 如果待复制对象属性值类型为数组，目标属性值类型不为数组的话，目标属性值就设为 []
		- 如果待复制对象属性值类型为对象，目标属性值类型不为对象的话，目标属性值就设为 {}
	-