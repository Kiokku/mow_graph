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
-