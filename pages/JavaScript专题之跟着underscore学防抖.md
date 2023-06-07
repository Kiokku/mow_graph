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
		-
	-