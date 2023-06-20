- > https://github.com/mqyqingfeng/Blog/issues/40
-
- ## each介绍
	- jQuery 的 each 方法，作为一个通用遍历方法，可用于[[#blue]]==遍历对象和数组==。
	- 语法为：`jQuery.each(object, [callback])`
- ## 退出循环
	- 尽管 ES5 提供了 forEach 方法，但是 forEach 没有办法中止或者跳出 forEach 循环，除了抛出一个异常。但是[[#red]]==对于 jQuery 的 each 函数，如果需要退出 each 循环可使回调函数返回 false，其它返回值将被忽略。==
	- ```
	  $.each( [0, 1, 2, 3, 4, 5], function(i, n){
	      if (i > 2) return false;
	      console.log( "Item #" + i + ": " + n );
	  });
	  
	  // Item #0: 0
	  // Item #1: 1
	  // Item #2: 2
	  ```
- ## 第一版
	- 首先，我们要根据参数的类型进行判断，[[#green]]==如果是数组，就调用 for 循环==，[[#blue]]==如果是对象，就使用 for in 循环==，[[#green]]==有一个例外是类数组对象，对于类数组对象，我们依然可以使用 for 循环==。
	- 那么又该如何判断类数组对象和数组呢？实际上，我们在[《JavaScript专题之类型判断(下)》](https://github.com/mqyqingfeng/Blog/issues/30)就讲过jQuery 数组和类数组对象判断函数 isArrayLike 的实现。
	- ```
	  // 第一版
	  function each(obj, callback) {
	      var length, i = 0;
	  
	      if ( isArrayLike(obj) ) {
	          length = obj.length;
	          for ( ; i < length; i++ ) {
	              callback(i, obj[i])
	          }
	      } else {
	          for ( i in obj ) {
	              callback(i, obj[i])
	          }
	      }
	  
	      return obj;
	  }
	  ```
-