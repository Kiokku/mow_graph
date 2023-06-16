- > https://github.com/mqyqingfeng/Blog/issues/36
-
- ## 扁平化
	- 数组的扁平化，就是将一个嵌套多层的数组 array (嵌套可以是任何层数)转换为只有一层的数组。
	- 举个例子，假设有个名为 flatten 的函数可以做到数组扁平化，效果就会如下：
	- ```
	  var arr = [1, [2, [3, 4]]];
	  console.log(flatten(arr)) // [1, 2, 3, 4]
	  ```
- ## 递归
	- ```
	  // 方法 1
	  var arr = [1, [2, [3, 4]]];
	  
	  function flatten(arr) {
	      var result = [];
	      for (var i = 0, len = arr.length; i < len; i++) {
	          if (Array.isArray(arr[i])) {
	              result = result.concat(flatten(arr[i]))
	          }
	          else {
	              result.push(arr[i])
	          }
	      }
	      return result;
	  }
	  
	  
	  console.log(flatten(arr))
	  ```
- ## toString
	- [[#blue]]==如果数组的元素都是数字，那么我们可以考虑使用 toString 方法==，因为：
	- `[1, [2, [3, 4]]].toString() // "1,2,3,4"`
	- 调用 toString 方法，返回了一个逗号分隔的扁平的字符串，这时候我们再 split，然后转成数字不就可以实现扁平化了吗？
	- ```
	  // 方法2
	  var arr = [1, [2, [3, 4]]];
	  
	  function flatten(arr) {
	      return arr.toString().split(',').map(function(item){
	          return +item
	      })
	  }
	  
	  console.log(flatten(arr))
	  ```
- ## reduce
	- 既然是对数组进行处理，最终返回一个值，我们就可以考虑使用 reduce 来简化代码：
	- ```
	  // 方法3
	  var arr = [1, [2, [3, 4]]];
	  
	  function flatten(arr) {
	      return arr.reduce(function(prev, next){
	          return prev.concat(Array.isArray(next) ? flatten(next) : next)
	      }, [])
	  }
	  
	  console.log(flatten(arr))
	  ```
- ## ES6扩展运算符
	- ```
	  // 方法4
	  var arr = [1, [2, [3, 4]]];
	  
	  function flatten(arr) {
	  
	      while (arr.some(item => Array.isArray(item))) {
	          arr = [].concat(...arr);
	      }
	  
	      return arr;
	  }
	  
	  console.log(flatten(arr))
	  ```
- ## undercore
	- ```
	  /**
	   * 数组扁平化
	   * @param  {Array} input   要处理的数组
	   * @param  {boolean} shallow 是否只扁平一层
	   * @param  {boolean} strict  是否严格处理元素，下面有解释
	   * @param  {Array} output  这是为了方便递归而传递的参数
	   * 源码地址：https://github.com/jashkenas/underscore/blob/master/underscore.js#L528
	   */
	  function flatten(input, shallow, strict, output) {
	  
	      // 递归使用的时候会用到output
	      output = output || [];
	      var idx = output.length;
	  
	      for (var i = 0, len = input.length; i < len; i++) {
	  
	          var value = input[i];
	          // 如果是数组，就进行处理
	          if (Array.isArray(value)) {
	              // 如果是只扁平一层，遍历该数组，依此填入 output
	              if (shallow) {
	                  var j = 0, length = value.length;
	                  while (j < length) output[idx++] = value[j++];
	              }
	              // 如果是全部扁平就递归，传入已经处理的 output，递归中接着处理 output
	              else {
	                  flatten(value, shallow, strict, output);
	                  idx = output.length;
	              }
	          }
	          // 不是数组，根据 strict 的值判断是跳过不处理还是放入 output
	          else if (!strict){
	              output[idx++] = value;
	          }
	      }
	  
	      return output;
	  
	  }
	  ```
	-