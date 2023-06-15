- >https://github.com/mqyqingfeng/Blog/issues/90
-
- ## 迭代器
	- 所谓**迭代器**，其实就是一个具有 `next()` 方法的对象，每次调用 `next()` 都会返回一个结果对象，该结果对象有两个属性，value 表示当前的值，done 表示遍历是否结束。
	- 我们直接用 ES5 的语法创建一个迭代器：
	- ```
	  function createIterator(items) {
	      var i = 0;
	      return {
	          next: function() {
	              var done = i >= item.length;
	              var value = !done ? items[i++] : undefined;
	  
	              return {
	                  done: done,
	                  value: value
	              };
	          }
	      };
	  }
	  
	  // iterator 就是一个迭代器对象
	  var iterator = createIterator([1, 2, 3]);
	  
	  console.log(iterator.next()); // { done: false, value: 1 }
	  console.log(iterator.next()); // { done: false, value: 2 }
	  console.log(iterator.next()); // { done: false, value: 3 }
	  console.log(iterator.next()); // { done: true, value: undefined }
	  ```
- ## for of
	- 除了迭代器之外，我们还需要一个可以遍历迭代器对象的方式，ES6 提供了 `for of` 语句，我们直接用 `for of` 遍历一下我们上节生成的遍历器对象试试：
	- ```
	  var iterator = createIterator([1, 2, 3]);
	  
	  for (let value of iterator) {
	      console.log(value);
	  }
	  ```
	- 结果报错 `TypeError: iterator is not iterable`，[[#red]]==表明我们生成的 iterator 对象并不是 iterable(可遍历的)==。
	- 其实一种数据结构[[#blue]]==只要部署了 Iterator 接口，我们就称这种数据结构是“可遍历的”（iterable）==。
	- ES6 规定，[[#blue]]==默认的 Iterator 接口部署在数据结构的 Symbol.iterator 属性==，或者说，一个数据结构只要具有 Symbol.iterator 属性，就可以认为是"可遍历的"（iterable）。
	- ```
	  const obj = {
	      value: 1
	  };
	  
	  obj[Symbol.iterator] = function() {
	      return createIterator([1, 2, 3]);
	  };
	  
	  for (value of obj) {
	      console.log(value);
	  }
	  
	  // 1
	  // 2
	  // 3
	  ```
	- 由此，我们也可以发现 for of 遍历的其实是对象的 Symbol.iterator 属性。
- ## 默认可遍历对象
	- 默认部署了 Symbol.iterator 属性的数据结构，即for...of 循环可以使用的范围包括：
		- 1. 数组
		  2. Set
		  3. Map
		  4. 类数组对象，如 arguments 对象、DOM NodeList 对象
		  5. Generator 对象
		  6. 字符串
- ## 模拟实现 for of
	- 通过 Symbol.iterator 属性获取迭代器对象，然后使用 while 遍历：
	- ```
	  function forOf(obj, cb) {
	      let iterable, result;
	  
	      if (typeof obj[Symbol.iterator] !== "function")
	          throw new TypeError(result + " is not iterable");
	      if (typeof cb !== "function") throw new TypeError("cb must be callable");
	  
	      iterable = obj[Symbol.iterator]();
	  
	      result = iterable.next();
	      while (!result.done) {
	          cb(result.value);
	          result = iterable.next();
	      }
	  }
	  ```
- ## 内建迭代器
	- 为了更好的访问对象中的内容，比如有的时候我们仅需要数组中的值，但有的时候不仅需要使用值还需要使用索引，ES6 为`数组`、`Map`、`Set` 集合内建了以下三种迭代器：
		- 1. `entries()`： 返回一个遍历器对象，用来遍历[key, value]组成的数组。对于数组，键名就是索引值。
		  2. keys() 返回一个遍历器对象，用来遍历所有的键名。
		  3. values() 返回一个遍历器对象，用来遍历所有的键值。
		-
	-