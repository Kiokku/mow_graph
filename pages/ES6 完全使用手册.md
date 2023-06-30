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
		- 需要拼接字符串的时候尽量改成使用模板字符串:
		- ```
		  // 例子 2-1
		  
		  // bad
		  const foo = 'this is a' + example;
		  
		  // good
		  const foo = `this is a ${example}`;
		  ```
	- ### 2. 标签模板
	  background-color:: blue
		- 可以借助标签模板优化书写方式:
		- ```
		  // 例子 2-2
		  
		  let url = oneLine `
		      www.taobao.com/example/index.html
		      ?foo=${foo}
		      &bar=${bar}
		  `;
		  
		  console.log(url); // www.taobao.com/example/index.html?foo=foo&bar=bar
		  ```
- ## 3. 箭头函数
	- 优先使用箭头函数，不过以下几种情况**避免使用**：
	- ### 1. 使用箭头函数定义对象的方法
	  background-color:: red
		- ```
		  // 例子 3-1
		  
		  // bad
		  let foo = {
		    value: 1,
		    getValue: () => console.log(this.value)
		  }
		  
		  foo.getValue();  // undefined
		  ```
	- ### 2. 定义原型方法
	  background-color:: red
		- ```
		  // 例子 3-2
		  
		  // bad
		  function Foo() {
		    this.value = 1
		  }
		  
		  Foo.prototype.getValue = () => console.log(this.value)
		  
		  let foo = new Foo()
		  foo.getValue();  // undefined
		  ```
	- ### 3. 作为事件的回调函数
	  background-color:: red
		- ```
		  // 例子 3-3
		  
		  // bad
		  const button = document.getElementById('myButton');
		  button.addEventListener('click', () => {
		      console.log(this === window); // => true
		      this.innerHTML = 'Clicked button';
		  });
		  ```
- ## 4. Symbol
	-