- >https://github.com/mqyqingfeng/Blog/issues/91
-
- ## 基本介绍
	- ES6 提供了新的数据结构 Set。
	- 它类似于数组，但是成员的值都是唯一的，没有重复的值。
- ## 初始化
	- Set 本身是一个构造函数，用来生成 Set 数据结构。
		- `let set = new Set();`
	- Set 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
	- ```
	  let set = new Set([1, 2, 3, 4, 4]);
	  console.log(set); // Set(4) {1, 2, 3, 4}
	  
	  set = new Set(document.querySelectorAll('div'));
	  console.log(set.size); // 66
	  
	  set = new Set(new Set([1, 2, 3, 4]));
	  console.log(set.size); // 4
	  ```
- ## 属性和方法
	- [[#green]]==操作方法有：==
		- 1. `add(value)`：添加某个值，返回 Set 结构本身。
		  2. `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
		  3. `has(value)`：返回一个布尔值，表示该值是否为 Set 的成员。
		  4. `clear()`：清除所有成员，无返回值。
	- [[#green]]==遍历方法有：==
		- 1. `keys()`：返回键名的遍历器
		  2. `values()`：返回键值的遍历器
		  3. `entries()`：返回键值对的遍历器
		  4. `forEach()`：使用回调函数遍历每个成员，无返回值
	- **注意 keys()、values()、entries() 返回的是遍历器**
	- ```
	  let set = new Set(['a', 'b', 'c']);
	  console.log(set.keys()); // SetIterator {"a", "b", "c"}
	  console.log([...set.keys()]); // ["a", "b", "c"]
	  
	  
	  console.log(set.values()); // SetIterator {"a", "b", "c"}
	  console.log([...set.values()]); // ["a", "b", "c"]
	  
	  console.log(set.entries()); // SetIterator {"a", "b", "c"}
	  console.log([...set.entries()]); // [["a", "a"], ["b", "b"], ["c", "c"]]
	  
	  set.forEach((value, key) => console.log(key + ': ' + value));
	  // 1: 1
	  // 2: 2
	  // 3: 3
	  ```
	- [[#blue]]==属性：==
		- 1. `Set.prototype.constructor`：构造函数，默认就是 Set 函数。
		  2. `Set.prototype.size`：返回 Set 实例的成员总数。
- ## 模拟实现第一版
	- 简单的 Set 数据结构，实现 `add`、`delete`、`has`、`clear`、`forEach` 方法
	- ```
	  /**
	   * 模拟实现第一版
	   */
	  (function(global) {
	  
	      function Set(data) {
	          this._values = [];
	          this.size = 0;
	  
	          data && data.forEach(function(item) {
	              this.add(item);
	          }, this);
	      }
	  
	      Set.prototype['add'] = function(value) {
	          if (this._values.indexOf(value) == -1) {
	              this._values.push(value);
	              ++this.size;
	          }
	          return this;
	      }
	  
	      Set.prototype['has'] = function(value) {
	          return (this._values.indexOf(value) !== -1);
	      }
	  
	      Set.prototype['delete'] = function(value) {
	          var idx = this._values.indexOf(value);
	          if (idx == -1) return false;
	          this._values.splice(idx, 1);
	          --this.size;
	          return true;
	      }
	  
	      Set.prototype['clear'] = function(value) {
	          this._values = [];
	          this.size = 0;
	      }
	  
	      Set.prototype['forEach'] = function(callbackFn, thisArg) {
	          thisArg = thisArg || global;
	          for (var i = 0; i < this._values.length; i++) {
	              callbackFn.call(thisArg, this._values[i], this._values[i], this);
	          }
	      }
	  
	      Set.length = 0;
	  
	      global.Set = Set;
	  
	  })(this)
	  ```
- ## 模拟实现第二版
	- 模拟实现的 Set 其实可以添加多个 NaN 而不会去重，然而对于真正的 Set 数据结构：
	- ```
	  let set = new Set();
	  set.add(NaN);
	  set.add(NaN);
	  console.log(set.size); // 1
	  ```
	- 所以我们[[#blue]]==需要对 NaN 这个值进行单独的处理==。
	- 处理的方式是当判断添加的值是 NaN 时，将其替换为一个独一无二的值，比如说一个很难重复的字符串类似于 `@@NaNValue`，当然了，说到独一无二的值，我们也可以直接使用 Symbol，代码如下：
	- ```
	  /**
	   * 模拟实现第二版
	   */
	  (function(global) {
	  
	      var NaNSymbol = Symbol('NaN');
	  
	      var encodeVal = function(value) {
	          return value !== value ? NaNSymbol : value;
	      }
	  
	      var decodeVal = function(value) {
	          return (value === NaNSymbol) ? NaN : value;
	      }
	  
	      function Set(data) {
	          this._values = [];
	          this.size = 0;
	  
	          data && data.forEach(function(item) {
	              this.add(item);
	          }, this);
	  
	      }
	  
	      Set.prototype['add'] = function(value) {
	          value = encodeVal(value);
	          if (this._values.indexOf(value) == -1) {
	              this._values.push(value);
	              ++this.size;
	          }
	          return this;
	      }
	  
	      Set.prototype['has'] = function(value) {
	          return (this._values.indexOf(encodeVal(value)) !== -1);
	      }
	  
	      Set.prototype['delete'] = function(value) {
	          var idx = this._values.indexOf(encodeVal(value));
	          if (idx == -1) return false;
	          this._values.splice(idx, 1);
	          --this.size;
	          return true;
	      }
	  
	      Set.prototype['clear'] = function(value) {
	          ...
	      }
	  
	      Set.prototype['forEach'] = function(callbackFn, thisArg) {
	          ...
	      }
	  
	      Set.length = 0;
	  
	      global.Set = Set;
	  
	  })(this)
	  ```
	-
	-