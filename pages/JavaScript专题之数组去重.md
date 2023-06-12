- > https://github.com/mqyqingfeng/Blog/issues/27
-
- ## 双层循环
	- [[#blue]]==最原始的方法==：
	- ```
	  var array = [1, 1, '1', '1'];
	  
	  function unique(array) {
	      // res用来存储结果
	      var res = [];
	      for (var i = 0, arrayLen = array.length; i < arrayLen; i++) {
	          for (var j = 0, resLen = res.length; j < resLen; j++ ) {
	              if (array[i] === res[j]) {
	                  break;
	              }
	          }
	          // 如果array[i]是唯一的，那么执行完循环，j等于resLen
	          if (j === resLen) {
	              res.push(array[i])
	          }
	      }
	      return res;
	  }
	  
	  console.log(unique(array)); // [1, "1"]
	  ```
- ## indexOf
	- ```
	  var array = [1, 1, '1'];
	  
	  function unique(array) {
	      var res = [];
	      for (var i = 0, len = array.length; i < len; i++) {
	          var current = array[i];
	          if (res.indexOf(current) === -1) {
	              res.push(current)
	          }
	      }
	      return res;
	  }
	  
	  console.log(unique(array));
	  ```
- ## 排序后去重
	- 先将要去重的数组使用 sort 方法排序后，相同的值就会被排在一起，然后我们就可以只判断当前元素与上一个元素是否相同，相同就说明重复，不相同就添加进 res，让我们写个 demo：
	- ```
	  var array = [1, 1, '1'];
	  
	  function unique(array) {
	      var res = [];
	      var sortedArray = array.concat().sort();
	      var seen;
	      for (var i = 0, len = sortedArray.length; i < len; i++) {
	          // 如果是第一个元素或者相邻的元素不相同
	          if (!i || seen !== sortedArray[i]) {
	              res.push(sortedArray[i])
	          }
	          seen = sortedArray[i];
	      }
	      return res;
	  }
	  
	  console.log(unique(array));
	  ```
	- 如果我们对一个已经排好序的数组去重，这种方法效率肯定高于使用 indexOf。
- ## unique API
	- 尝试写一个名为 unique 的工具函数，我们根据一个**参数 isSorted** 判断传入的数组是否是已排序的，如果为 true，我们就判断相邻元素是否相同，如果为 false，我们就使用 indexOf 进行判断
	- ```
	  var array1 = [1, 2, '1', 2, 1];
	  var array2 = [1, 1, '1', 2, 2];
	  
	  // 第一版
	  function unique(array, isSorted) {
	      var res = [];
	      var seen = [];
	  
	      for (var i = 0, len = array.length; i < len; i++) {
	          var value = array[i];
	          if (isSorted) {
	              if (!i || seen !== value) {
	                  res.push(value)
	              }
	              seen = value;
	          }
	          else if (res.indexOf(value) === -1) {
	              res.push(value);
	          }        
	      }
	      return res;
	  }
	  
	  console.log(unique(array1)); // [1, 2, "1"]
	  console.log(unique(array2, true)); // [1, "1", 2]
	  ```
- ## 优化
	- 新需求：字母的大小写视为一致，比如'a'和'A'，保留一个就可以了！
	- 虽然我们可以先处理数组中的所有数据，比如将所有的字母转成小写，然后再传入unique函数，但是有没有方法可以省掉处理数组的这一遍循环，直接就在去重的循环中做呢？让我们去完成这个需求：
	- ```
	  var array3 = [1, 1, 'a', 'A', 2, 2];
	  
	  // 第二版
	  // iteratee 英文释义：迭代 重复
	  function unique(array, isSorted, iteratee) {
	      var res = [];
	      var seen = [];
	  
	      for (var i = 0, len = array.length; i < len; i++) {
	          var value = array[i];
	          var computed = iteratee ? iteratee(value, i, array) : value;
	          if (isSorted) {
	              if (!i || seen !== computed) {
	                  res.push(value)
	              }
	              seen = computed;
	          }
	          else if (iteratee) {
	              if (seen.indexOf(computed) === -1) {
	                  seen.push(computed);
	                  res.push(value);
	              }
	          }
	          else if (res.indexOf(value) === -1) {
	              res.push(value);
	          }        
	      }
	      return res;
	  }
	  
	  console.log(unique(array3, false, function(item){
	      return typeof item == 'string' ? item.toLowerCase() : item
	  })); // [1, "a", 2]
	  ```
	- 函数传递三个参数：
		- `array`：表示要去重的数组，必填
		- `isSorted`：表示函数传入的数组是否已排过序，如果为 true，将会采用更快的方法进行去重
		- `iteratee`：传入一个函数，可以对每个元素进行重新的计算，然后根据处理的结果进行去重
	- 至此，我们已经仿照着 underscore 的思路写了一个 unique 函数，具体可以查看 [Github](https://github.com/jashkenas/underscore/blob/master/underscore.js#L562)。
- ## filter
	-