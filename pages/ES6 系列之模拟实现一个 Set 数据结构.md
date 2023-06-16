- >https://github.com/mqyqingfeng/Blog/issues/91
-
- ## 基本介绍
	- ES6 提供了新的数据结构 Set。
	- 它类似于数组，但是成员的值都是唯一的，没有重复的值。
- ## 初始化
	- Set 本身是一个构造函数，用来生成 Set 数据结构。
		- `let set = new Set();`
	- Set 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
	- ```
	  let set = new Set([1, 2, 3, 4, 4]);
	  console.log(set); // Set(4) {1, 2, 3, 4}
	  
	  set = new Set(document.querySelectorAll('div'));
	  console.log(set.size); // 66
	  
	  set = new Set(new Set([1, 2, 3, 4]));
	  console.log(set.size); // 4
	  ```
- ## 属性和方法
	- [[#green]]==操作方法有：==
		- 1. `add(value)`：添加某个值，返回 Set 结构本身。
		  2. `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
		  3. `has(value)`：返回一个布尔值，表示该值是否为 Set 的成员。
		  4. `clear()`：清除所有成员，无返回值。
	- [[#green]]==遍历方法有：==
		- 1. `keys()`：返回键名的遍历器
		  2. `values()`：返回键值的遍历器
		  3. `entries()`：返回键值对的遍历器
		  4. `forEach()`：使用回调函数遍历每个成员，无返回值
	- **注意 keys()、values()、entries() 返回的是遍历器**
	-