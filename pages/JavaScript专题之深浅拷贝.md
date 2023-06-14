- > https://github.com/mqyqingfeng/Blog/issues/32
-
- ## 数组的浅拷贝
	- 如果是数组，我们可以利用数组的一些方法比如：`slice`、`concat` 返回一个新数组的特性来实现拷贝。
	- ```
	  var arr = ['old', 1, true, null, undefined];
	  
	  var new_arr = arr.concat();
	  
	  new_arr[0] = 'new';
	  
	  console.log(arr) // ["old", 1, true, null, undefined]
	  console.log(new_arr) // ["new", 1, true, null, undefined]
	  ```
	- 用 slice 可以这样做：
	- ```
	  var new_arr = arr.slice();
	  ```
	- 但是[[#red]]==如果数组嵌套了对象或者数组==的话，比如：
	- ```
	  var arr = [{old: 'old'}, ['old']];
	  
	  var new_arr = arr.concat();
	  
	  arr[0].old = 'new';
	  arr[1][0] = 'new';
	  
	  console.log(arr) // [{old: 'new'}, ['new']]
	  console.log(new_arr) // [{old: 'new'}, ['new']]
	  ```
	-