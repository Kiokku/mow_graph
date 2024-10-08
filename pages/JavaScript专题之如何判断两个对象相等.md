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
	- 如果我们添加上了`typeof b !== function`这句，当 a 是基本类型，而 b 是函数的时候，就会进入 `deepEq 函数`，而去掉这一句，就会进入直接进入 false，实际上 基本类型和函数肯定是不会相等的，所以[[#green]]==这样做代码又少，又可以让一种情况更早退出==。
- ## String 对象
	- `Object.prototype.toString`判断类型：
	- ```
	  var toString = Object.prototype.toString;
	  toString.call('Curly'); // "[object String]"
	  toString.call(new String('Curly')); // "[object String]"
	  ```
	- [[#blue]]==隐式类型转换==判断字符串和字符串包装对象是相等:
	- ```
	  console.log('Curly' + '' === new String('Curly') + ''); // true
	  ```
- ## 更多对象
	- **Boolean**
		- ```
		  var a = true;
		  var b = new Boolean(true);
		  
		  console.log(+a === +b) // true
		  ```
	- **Date**
		- ```
		  var a = new Date(2009, 9, 25);
		  var b = new Date(2009, 9, 25);
		  
		  console.log(+a === +b) // true
		  ```
	- **RegExp**
		- ```
		  var a = /a/i;
		  var b = new RegExp(/a/i);
		  
		  console.log('' + a === '' + b) // true
		  ```
	- **Number**
		- ```
		  var a = Number(NaN);
		  var b = Number(NaN);
		  
		  function eq() {
		      // 判断 Number(NaN) Object(NaN) 等情况
		      if (+a !== +a) return +b !== +b;
		      // 其他判断 ...
		  }
		  
		  console.log(eq(a, b)); // true
		  ```
- ## deepEq 函数
	- ```
	  var toString = Object.prototype.toString;
	  
	  function deepEq(a, b) {
	      var className = toString.call(a);
	      if (className !== toString.call(b)) return false;
	  
	      switch (className) {
	          case '[object RegExp]':
	          case '[object String]':
	              return '' + a === '' + b;
	          case '[object Number]':
	              if (+a !== +a) return +b !== +b;
	              return +a === 0 ? 1 / +a === 1 / b : +a === +b;
	        case '[object Date]':
	        case '[object Boolean]':
	              return +a === +b;
	      }
	  
	      // 其他判断
	  }
	  ```
- ## 构造函数实例相等
	- 我们看个例子：
	- ```
	  function Person() {
	      this.name = name;
	  }
	  
	  function Animal() {
	      this.name = name
	  }
	  
	  var person = new Person('Kevin');
	  var animal = new Animal('Kevin');
	  
	  eq(person, animal) // ???
	  ```
	- 虽然 `person` 和 `animal` 都是 `{name: 'Kevin'}`，但是 `person` 和 `animal` [[#red]]==属于不同**构造函数**的实例，为了做出区分，我们认为是不同的对象。==
	- ```
	  function isFunction(obj) {
	      return toString.call(obj) === '[object Function]'
	  }
	  
	  function deepEq(a, b) {
	      // 接着上面的内容
	      var areArrays = className === '[object Array]';
	      // 不是数组
	      if (!areArrays) {
	          // 过滤掉两个函数的情况
	          if (typeof a != 'object' || typeof b != 'object') return false;
	  
	          var aCtor = a.constructor, bCtor = b.constructor;
	          // aCtor 和 bCtor 必须都存在并且都不是 Object 构造函数的情况下，aCtor 不等于 bCtor， 那这两个对象就真的不相等啦
	          if (aCtor !== bCtor && !(isFunction(aCtor) && aCtor instanceof aCtor && isFunction(bCtor) && bCtor instanceof bCtor) && ('constructor' in a && 'constructor' in b)) {
	              return false;
	          }
	      }
	  
	      // 下面还有好多判断
	  }
	  ```
- ## 数组相等
	- 递归遍历：
	- ```
	  function deepEq(a, b) {
	      // 再接着上面的内容
	      if (areArrays) {
	  
	          length = a.length;
	          if (length !== b.length) return false;
	  
	          while (length--) {
	              if (!eq(a[length], b[length])) return false;
	           }
	      } 
	      else {
	  
	          var keys = Object.keys(a), key;
	          length = keys.length;
	  
	          if (Object.keys(b).length !== length) return false;
	  
	          while (length--) {
	              key = keys[length];
	              if (!(b.hasOwnProperty(key) && eq(a[key], b[key]))) return false;
	          }
	      }
	      return true;
	  
	  }
	  ```
- ## 循环引用
	- 举个简单的例子：
	- ```
	  a = {abc: null};
	  b = {abc: null};
	  a.abc = a;
	  b.abc = b;
	  
	  eq(a, b)
	  ```
	- underscore 的思路是 eq 的时候，[[#blue]]==多传递两个参数为 aStack 和 bStack，用来储存 a 和 b 递归比较过程中的 a 和 b 的值==
- ## 最终的 eq 函数
	- ```
	  var toString = Object.prototype.toString;
	  
	  function isFunction(obj) {
	      return toString.call(obj) === '[object Function]'
	  }
	  
	  function eq(a, b, aStack, bStack) {
	  
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
	      return deepEq(a, b, aStack, bStack);
	  };
	  
	  function deepEq(a, b, aStack, bStack) {
	  
	      // a 和 b 的内部属性 [[class]] 相同时 返回 true
	      var className = toString.call(a);
	      if (className !== toString.call(b)) return false;
	  
	      switch (className) {
	          case '[object RegExp]':
	          case '[object String]':
	              return '' + a === '' + b;
	          case '[object Number]':
	              if (+a !== +a) return +b !== +b;
	              return +a === 0 ? 1 / +a === 1 / b : +a === +b;
	          case '[object Date]':
	          case '[object Boolean]':
	              return +a === +b;
	      }
	  
	      var areArrays = className === '[object Array]';
	      // 不是数组
	      if (!areArrays) {
	          // 过滤掉两个函数的情况
	          if (typeof a != 'object' || typeof b != 'object') return false;
	  
	          var aCtor = a.constructor,
	              bCtor = b.constructor;
	          // aCtor 和 bCtor 必须都存在并且都不是 Object 构造函数的情况下，aCtor 不等于 bCtor， 那这两个对象就真的不相等啦
	          if (aCtor !== bCtor && !(isFunction(aCtor) && aCtor instanceof aCtor && isFunction(bCtor) && bCtor instanceof bCtor) && ('constructor' in a && 'constructor' in b)) {
	              return false;
	          }
	      }
	  
	  
	      aStack = aStack || [];
	      bStack = bStack || [];
	      var length = aStack.length;
	  
	      // 检查是否有循环引用的部分
	      while (length--) {
	          if (aStack[length] === a) {
	              return bStack[length] === b;
	          }
	      }
	  
	      aStack.push(a);
	      bStack.push(b);
	  
	      // 数组判断
	      if (areArrays) {
	  
	          length = a.length;
	          if (length !== b.length) return false;
	  
	          while (length--) {
	              if (!eq(a[length], b[length], aStack, bStack)) return false;
	          }
	      }
	      // 对象判断
	      else {
	  
	          var keys = Object.keys(a),
	              key;
	          length = keys.length;
	  
	          if (Object.keys(b).length !== length) return false;
	          while (length--) {
	  
	              key = keys[length];
	              if (!(b.hasOwnProperty(key) && eq(a[key], b[key], aStack, bStack))) return false;
	          }
	      }
	  
	      aStack.pop();
	      bStack.pop();
	      return true;
	  
	  }
	  
	  console.log(eq(0, 0)) // true
	  console.log(eq(0, -0)) // false
	  
	  console.log(eq(NaN, NaN)); // true
	  console.log(eq(Number(NaN), Number(NaN))); // true
	  
	  console.log(eq('Curly', new String('Curly'))); // true
	  
	  console.log(eq([1], [1])); // true
	  console.log(eq({ value: 1 }, { value: 1 })); // true
	  
	  var a, b;
	  
	  a = { foo: { b: { foo: { c: { foo: null } } } } };
	  b = { foo: { b: { foo: { c: { foo: null } } } } };
	  a.foo.b.foo.c.foo = a;
	  b.foo.b.foo.c.foo = b;
	  
	  console.log(eq(a, b)) // true
	  ```