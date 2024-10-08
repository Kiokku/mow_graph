- > https://github.com/mqyqingfeng/Blog/issues/37
-
- ## findIndex
	- ES6 对数组新增了 findIndex 方法，它会返回数组中满足提供的函数的第一个元素的索引，否则返回 -1。
- ## 实现findIndex
	- ```
	  function findIndex(array, predicate, context) {
	      for (var i = 0; i < array.length; i++) {
	          if (predicate.call(context, array[i], i, array)) return i;
	      }
	      return -1;
	  }
	  
	  console.log(findIndex([1, 2, 3, 4], function(item, i, array){
	      if (item == 3) return true;
	  })) // 2
	  ```
- ## findLastIndex
	- findIndex 是正序查找，但正如 indexOf 还有一个对应的 lastIndexOf 方法，我们也想写一个倒序查找的 findLastIndex 函数。实现自然也很简单，只要修改下循环即可。
	- ```
	  function findLastIndex(array, predicate, context) {
	      var length = array.length;
	      for (var i = length - 1; i >= 0; i--) {
	          if (predicate.call(context, array[i], i, array)) return i;
	      }
	      return -1;
	  }
	  
	  console.log(findLastIndex([1, 2, 3, 4], function(item, index, array){
	      if (item == 1) return true;
	  })) // 0
	  ```
- ## createIndexFinder
	- findIndex 和 findLastIndex 其实有很多重复的部分，如何精简冗余的内容呢？
	- ```
	  function createIndexFinder(dir) {
	      return function(array, predicate, context) {
	  
	          var length = array.length;
	          var index = dir > 0 ? 0 : length - 1;
	  
	          for (; index >= 0 && index < length; index += dir) {
	              if (predicate.call(context, array[index], index, array)) return index;
	          }
	  
	          return -1;
	      }
	  }
	  
	  var findIndex = createIndexFinder(1);
	  var findLastIndex = createIndexFinder(-1);
	  ```
- ## sortedIndex
	- [[#blue]]==新需求：==在一个排好序的数组中找到 value 对应的位置，保证插入数组后，依然保持有序的状态。
	- 假设该函数命名为 sortedIndex，效果为：`sortedIndex([10, 20, 30], 25); // 2`
	- [[#blue]]==使用二分查找法，确定值的位置：==
		- ```
		  // 第一版
		  function sortedIndex(array, obj) {
		  
		      var low = 0, high = array.length;
		  
		      while (low < high) {
		          var mid = Math.floor((low + high) / 2);
		          if (array[mid] < obj) low = mid + 1;
		          else high = mid;
		      }
		  
		      return high;
		  };
		  
		  console.log(sortedIndex([10, 20, 30, 40, 50], 35)) // 3
		  ```
	- 现在的方法虽然能用，但通用性不够，比如我们希望能处理这样的情况：
	- ```
	  // stooges 配角 比如 三个臭皮匠 The Three Stooges
	  var stooges = [{name: 'stooge1', age: 10}, {name: 'stooge2', age: 30}];
	  
	  var result = sortedIndex(stooges, {name: 'stooge3', age: 20}, function(stooge){
	      return stooge.age
	  });
	  
	  console.log(result) // 1
	  ```
	- 所以我们还需要再加上一个参数 iteratee 函数对数组的每一个元素进行处理，一般这个时候，还会涉及到 this 指向的问题，所以我们再传一个 context 来让我们可以指定 this，那么这样一个函数又该如何写呢？
	- ```
	  // 第二版
	  function cb(func, context) {
	         if (context === void 0) return func;
	         return function() {
	             return func.apply(context, arguments);
	        };
	  }
	  
	  function sortedIndex(array, obj, iteratee, context) {
	  
	      iteratee = cb(iteratee, context)
	  
	      var low = 0, high = array.length;
	      while (low < high) {
	          var mid = Math.floor((low + high) / 2);
	          if (iteratee(array[mid]) < iteratee(obj)) low = mid + 1;
	          else high = mid;
	      }
	      return high;
	  };
	  ```
- ## indexOf
	- ```
	  // 第一版
	  function createIndexOfFinder(dir) {
	      return function(array, item){
	          var length = array.length;
	          var index = dir > 0 ? 0 : length - 1;
	          for (; index >= 0 && index < length; index += dir) {
	              if (array[index] === item) return index;
	          }
	          return -1;
	      }
	  }
	  
	  var indexOf = createIndexOfFinder(1);
	  var lastIndexOf = createIndexOfFinder(-1);
	  
	  var result = indexOf([1, 2, 3, 4, 5], 2);
	  
	  console.log(result) // 1
	  ```
- ## fromIndex
	- 数组的 indexOf 方法也可以多传递一个参数 fromIndex，从 MDN 中看到 fromIndex 的讲究可有点多：
	- > 设定开始查找的位置。如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回 -1。如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即 -1 表示从最后一个元素开始查找，-2 表示从倒数第二个元素开始查找 ，以此类推。 注意：如果参数中提供的索引值是一个负值，仍然从前向后查询数组。如果抵消后的索引值仍小于 0，则整个数组都将会被查询。其默认值为 0。
	- lastIndexOf 的 fromIndex：
	- > 从此位置开始逆向查找。默认为数组的长度减 1，即整个数组都被查找。如果该值大于或等于数组的长度，则整个数组会被查找。如果为负值，将其视为从数组末尾向前的偏移。即使该值为负，数组仍然会被从后向前查找。如果该值为负时，其绝对值大于数组长度，则方法返回 -1，即数组不会被查找。
	- ```
	  // 第二版
	  function createIndexOfFinder(dir) {
	  
	      return function(array, item, idx){
	          var length = array.length;
	          var i = 0;
	  
	          if (typeof idx == "number") {
	              if (dir > 0) {
	                  i = idx >= 0 ? idx : Math.max(length + idx, 0);
	              }
	              else {
	                  length = idx >= 0 ? Math.min(idx + 1, length) : idx + length + 1;
	              }
	          }
	  
	          for (idx = dir > 0 ? i : length - 1; idx >= 0 && idx < length; idx += dir) {
	              if (array[idx] === item) return idx;
	          }
	          return -1;
	      }
	  }
	  
	  var indexOf = createIndexOfFinder(1);
	  var lastIndexOf = createIndexOfFinder(-1);
	  ```
- ## 优化
	- 第一个优化是[[#blue]]==支持查找 NaN==。
		- `[1, NaN].indexOf(NaN) // -1`
		- ```
		  // 第三版
		  function createIndexOfFinder(dir, predicate) {
		  
		      return function(array, item, idx){
		  
		          if () { ... }
		  
		          // 判断元素是否是 NaN
		          if (item !== item) {
		              // 在截取好的数组中查找第一个满足isNaN函数的元素的下标
		              idx = predicate(array.slice(i, length), isNaN)
		              return idx >= 0 ? idx + i: -1;
		          }
		  
		          for () { ... }
		      }
		  }
		  
		  var indexOf = createIndexOfFinder(1, findIndex);
		  var lastIndexOf = createIndexOfFinder(-1, findLastIndex);
		  ```
	- 第二个优化是[[#blue]]==支持对有序的数组进行更快的二分查找。==
		- 如果 indexOf 第三个参数不传开始搜索的下标值，而是一个布尔值 true，就认为数组是一个排好序的数组，这时候，就会采用更快的二分法进行查找，这个时候，可以利用我们写的 sortedIndex 函数。
		- ```
		  // 第四版
		  function createIndexOfFinder(dir, predicate, sortedIndex) {
		  
		      return function(array, item, idx){
		          var length = array.length;
		          var i = 0;
		  
		          if (typeof idx == "number") {
		              if (dir > 0) {
		                  i = idx >= 0 ? idx : Math.max(length + idx, 0);
		              }
		              else {
		                  length = idx >= 0 ? Math.min(idx + 1, length) : idx + length + 1;
		              }
		          }
		          else if (sortedIndex && idx && length) {
		              idx = sortedIndex(array, item);
		              // 如果该插入的位置的值正好等于元素的值，说明是第一个符合要求的值
		              return array[idx] === item ? idx : -1;
		          }
		  
		          // 判断是否是 NaN
		          if (item !== item) {
		              idx = predicate(array.slice(i, length), isNaN)
		              return idx >= 0 ? idx + i: -1;
		          }
		  
		          for (idx = dir > 0 ? i : length - 1; idx >= 0 && idx < length; idx += dir) {
		              if (array[idx] === item) return idx;
		          }
		          return -1;
		      }
		  }
		  
		  var indexOf = createIndexOfFinder(1, findIndex, sortedIndex);
		  var lastIndexOf = createIndexOfFinder(-1, findLastIndex);
		  ```
	-
-