- > https://github.com/mqyqingfeng/Blog/issues/111
-
- ## 1. let 和 const
	- 在我们开发的时候，可能认为应该默认使用 let 而不是 var，这种情况下，对于需要写保护的变量要使用 const。
	- 然而另一种做法日益普及：[[#green]]==默认使用 const，只有当确实需要改变变量的值的时候才使用 let==。这是因为大部分的变量的值在初始化后不应再改变，而预料之外的变量的修改是很多 bug 的源头。
	- ```
	  // 例子 1-1
	  
	  // bad
	  var foo = 'bar';
	  
	  // good
	  let foo = 'bar';
	  
	  // better
	  const foo = 'bar';
	  ```
- ## 2. 模板字符串
	- ### 1. 模板字符串
	  background-color:: blue
		- ```
		  ```