- > https://github.com/mqyqingfeng/Blog/issues/87
-
- ## 回顾
	- ES6 引入了一种新的原始数据类型 Symbol，表示独一无二的值。
	- **1. Symbol 值通过 Symbol 函数生成，使用 typeof，结果为 "symbol"**
		- ```
		  var s = Symbol();
		  console.log(typeof s); // "symbol"
		  ```
	- **2. Symbol 函数前不能使用 new 命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。**
	- **3. instanceof 的结果为 false**
		- ```
		  var s = Symbol('foo');
		  console.log(s instanceof Symbol); // false
		  ```
	- **4. Symbol 函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。**
		- ```
		  var s1 = Symbol('foo');
		  console.log(s1); // Symbol(foo)
		  ```
	- **5. 如果 Symbol 的参数是一个对象，就会调用该对象的 toString 方法，将其转为字符串，然后才生成一个 Symbol 值。**
		- ```
		  const obj = {
		    toString() {
		      return 'abc';
		    }
		  };
		  const sym = Symbol(obj);
		  console.log(sym); // Symbol(abc)
		  ```
	- **6. Symbol 函数的参数只是表示对当前 Symbol 值的描述，相同参数的 Symbol 函数的返回值是不相等的。**
		- ```
		  // 没有参数的情况
		  var s1 = Symbol();
		  var s2 = Symbol();
		  
		  console.log(s1 === s2); // false
		  
		  // 有参数的情况
		  var s1 = Symbol('foo');
		  var s2 = Symbol('foo');
		  
		  console.log(s1 === s2); // false
		  ```
	- **7. Symbol 值不能与其他类型的值进行运算，会报错。**
		- ```
		  var sym = Symbol('My symbol');
		  
		  console.log("your symbol is " + sym); // TypeError: can't convert symbol to string
		  ```
	- **8. Symbol 值可以显式转为字符串。**
		- ```
		  var sym = Symbol('My symbol');
		  
		  console.log(String(sym)); // 'Symbol(My symbol)'
		  console.log(sym.toString()); // 'Symbol(My symbol)'
		  ```
	- **9. Symbol 值可以作为标识符，用于对象的属性名，可以保证不会出现同名的属性。**
		- ```
		  var mySymbol = Symbol();
		  
		  // 第一种写法
		  var a = {};
		  a[mySymbol] = 'Hello!';
		  
		  // 第二种写法
		  var a = {
		    [mySymbol]: 'Hello!'
		  };
		  
		  // 第三种写法
		  var a = {};
		  Object.defineProperty(a, mySymbol, { value: 'Hello!' });
		  
		  // 以上写法都得到同样结果
		  console.log(a[mySymbol]); // "Hello!"
		  ```
	- **10. Symbol 作为属性名，该属性不会出现在 for...in、for...of 循环中，也不会被 Object.keys()、Object.getOwnPropertyNames()、JSON.stringify() 返回。但是，它也不是私有属性，有一个 Object.getOwnPropertySymbols 方法，可以获取指定对象的所有 Symbol 属性名。**
		- ```
		  var obj = {};
		  var a = Symbol('a');
		  var b = Symbol('b');
		  
		  obj[a] = 'Hello';
		  obj[b] = 'World';
		  
		  var objectSymbols = Object.getOwnPropertySymbols(obj);
		  
		  console.log(objectSymbols);
		  // [Symbol(a), Symbol(b)]
		  ```
	- **11. 如果我们希望使用同一个 Symbol 值，可以使用 Symbol.for。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建并返回一个以该字符串为名称的 Symbol 值。**
		- ```
		  var s1 = Symbol.for('foo');
		  var s2 = Symbol.for('foo');
		  var s3 = Symbol('foo');
		  
		  console.log(s1 === s2); // true
		  console.log(s1 === s3); // false
		  ```
	- **12. Symbol.keyFor 方法返回一个已登记的 Symbol 类型值的 key。**
		- ```
		  var s1 = Symbol.for("foo");
		  console.log(Symbol.keyFor(s1)); // "foo"
		  
		  var s2 = Symbol("foo");
		  console.log(Symbol.keyFor(s2) ); // undefined
		  ```
- ## 分析
	- 如果我们要模拟实现一个 Symbol 的话，基本的思路就是构建一个 Symbol 函数，然后直接返回一个独一无二的值。
	- 当调用 Symbol 的时候，会采用以下步骤：
		- 1. 如果使用 new ，就报错
		  2. 如果 description 是 undefined，让 descString 为 undefined
		  3. 否则 让 descString 为 ToString(description)
		  4. 如果报错，就返回
		  5. 返回一个新的唯一的 Symbol 值，它的内部属性 `[[Description]]` 值为 descString
	- 考虑到还需要定义一个 `[[Description]]` 属性，如果直接返回一个基本类型的值，是无法做到这一点的，所以我们最终还是返回一个对象。
- ## 第一版
	- ```
	  // 第一版
	  (function() {
	      var root = this;
	  
	      var SymbolPolyfill = function Symbol(description) {
	  
	          // 实现特性第 2 点：Symbol 函数前不能使用 new 命令
	          if (this instanceof SymbolPolyfill) throw new TypeError('Symbol is not a constructor');
	  
	          // 实现特性第 5 点：如果 Symbol 的参数是一个对象，就会调用该对象的 toString 方法，将其转为字符串，然后才生成一个 Symbol 值。
	          var descString = description === undefined ? undefined : String(description)
	  
	          var symbol = Object.create(null)
	  
	          Object.defineProperties(symbol, {
	              '__Description__': {
	                  value: descString,
	                  writable: false,
	                  enumerable: false,
	                  configurable: false
	              }
	          });
	  
	          // 实现特性第 6 点，因为调用该方法，返回的是一个新对象，两个对象之间，只要引用不同，就不会相同
	          return symbol;
	      }
	  
	      root.SymbolPolyfill = SymbolPolyfill;
	  })();
	  ```
	-
-