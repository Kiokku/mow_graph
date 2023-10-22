- > https://github.com/mqyqingfeng/Blog/issues/22
-
- ## 防抖debounce
	- ### 第一版
	  background-color:: blue
		- ```
		  // 第一版
		  function debounce(func, wait) {
		      var timeout;
		      return function () {
		          clearTimeout(timeout)
		          timeout = setTimeout(func, wait);
		      }
		  }
		  ```
	- ### this
	  background-color:: blue
		- 如果使用我们的 debounce 函数，this 就会指向 Window 对象！
		- 所以我们需要将 this 指向正确的对象。
		- ```
		  // 第二版
		  function debounce(func, wait) {
		      var timeout;
		  
		      return function () {
		          var context = this;
		  
		          clearTimeout(timeout)
		          timeout = setTimeout(function(){
		              func.apply(context)
		          }, wait);
		      }
		  }
		  ```
	- ### event 对象
	  background-color:: blue
		- JavaScript 在事件处理函数中会提供事件对象 event，在我们实现的 debounce 函数中，却只会打印 undefined!
		- // 第三版
		  function debounce(func, wait) {
		      var timeout;
		  
		      return function () {
		          var context = this;
		          var args = arguments;
		  
		          clearTimeout(timeout)
		          timeout = setTimeout(function(){
		              func.apply(context, args)
		          }, wait);
		      }
		  }
		- 到此为止，我们修复了两个小问题：
			- 1. this 指向
			  2. event 对象
	- ### 立刻执行
	  background-color:: blue
		- 为了让这个函数更加完善，我们接下来思考一个新的需求。
		- **我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。**
		- 我们加个 immediate 参数判断是否是立刻执行。
		- ```
		  // 第四版
		  function debounce(func, wait, immediate) {
		  
		      var timeout;
		  
		      return function () {
		          var context = this;
		          var args = arguments;
		  
		          if (timeout) clearTimeout(timeout);
		          if (immediate) {
		              // 如果已经执行过，不再执行
		              var callNow = !timeout;
		              timeout = setTimeout(function(){
		                  timeout = null;
		              }, wait)
		              if (callNow) func.apply(context, args)
		          }
		          else {
		              timeout = setTimeout(function(){
		                  func.apply(context, args)
		              }, wait);
		          }
		      }
		  }
		  ```
	- ### 返回值
	  background-color:: blue
		- 要返回函数的执行结果，但是当 immediate 为 false 的时候，因为使用了 setTimeout ，我们将 func.apply(context, args) 的返回值赋给变量，最后再 return 的时候，值将会一直是 undefined，所以我们只在 immediate 为 true 的时候返回函数的执行结果。
		- ```
		  // 第五版
		  function debounce(func, wait, immediate) {
		  
		      var timeout, result;
		  
		      return function () {
		          var context = this;
		          var args = arguments;
		  
		          if (timeout) clearTimeout(timeout);
		          if (immediate) {
		              // 如果已经执行过，不再执行
		              var callNow = !timeout;
		              timeout = setTimeout(function(){
		                  timeout = null;
		              }, wait)
		              if (callNow) result = func.apply(context, args)
		          }
		          else {
		              timeout = setTimeout(function(){
		                  func.apply(context, args)
		              }, wait);
		          }
		          return result;
		      }
		  }
		  ```
	- ### 取消
	  background-color:: blue
		- 取消 debounce 函数
		- ```
		  // 第六版
		  function debounce(func, wait, immediate) {
		  
		      var timeout, result;
		  
		      var debounced = function () {
		          var context = this;
		          var args = arguments;
		  
		          if (timeout) clearTimeout(timeout);
		          if (immediate) {
		              // 如果已经执行过，不再执行
		              var callNow = !timeout;
		              timeout = setTimeout(function(){
		                  timeout = null;
		              }, wait)
		              if (callNow) result = func.apply(context, args)
		          }
		          else {
		              timeout = setTimeout(function(){
		                  func.apply(context, args)
		              }, wait);
		          }
		          return result;
		      };
		  
		      debounced.cancel = function() {
		          clearTimeout(timeout);
		          timeout = null;
		      };
		  
		      return debounced;
		  }
		  ```