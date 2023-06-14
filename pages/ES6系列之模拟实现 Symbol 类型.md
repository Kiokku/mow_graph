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
	-