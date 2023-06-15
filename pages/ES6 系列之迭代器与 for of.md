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
-