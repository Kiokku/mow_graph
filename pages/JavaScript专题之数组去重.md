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
-