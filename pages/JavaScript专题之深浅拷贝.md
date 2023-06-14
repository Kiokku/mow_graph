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
	- 我们会发现，无论是新数组还是旧数组都发生了变化，也就是说[[#red]]==使用 concat 方法，克隆的并不彻底==。
	- [[#blue]]==如果数组元素是基本类型，就会拷贝一份，互不影响，==[[#red]]==而如果是对象或者数组，就会只拷贝对象和数组的引用==，这样我们无论在新旧数组进行了修改，两者都会发生变化。
	- 我们把这种[[#green]]==复制引用的拷贝方法称之为浅拷贝==，与之对应的就是深拷贝，[[#green]]==深拷贝就是指完全的拷贝一个对象==，即使嵌套了对象，两者也相互分离，修改一个对象的属性，也不会影响另一个。
	- [[#green]]==concat 和 slice 是一种浅拷贝。==
- ## 数组的深拷贝
	- 这里介绍一个技巧，不仅适用于数组还适用于对象！那就是`JSON.parse``JSON.stringfy`：
	- ```
	  var arr = ['old', 1, true, ['old1', 'old2'], {old: 1}]
	  
	  var new_arr = JSON.parse( JSON.stringify(arr) );
	  
	  console.log(new_arr);
	  ```
	- [[#red]]==就是有一个问题，不能拷贝函数==，我们做个试验：
	- ```
	  var arr = [function(){
	      console.log(a)
	  }, {
	      b: function(){
	          console.log(b)
	      }
	  }]
	  
	  var new_arr = JSON.parse(JSON.stringify(arr));
	  
	  console.log(new_arr);
	  ```
- ## 浅拷贝的实现
	- 遍历对象，然后把属性和属性值都放在一个新的对象:
	- ```
	  ```