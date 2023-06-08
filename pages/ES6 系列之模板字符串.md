- > https://github.com/mqyqingfeng/Blog/issues/84
-
- ## 基础用法
	- ```
	  let message = `Hello World`;
	  console.log(message);
	  ```
	- 如果你碰巧要在字符串中使用**反撇号**，你可以使用反斜杠转义：
	- ```
	  let message = `Hello \` World`;
	  console.log(message);
	  ```
	- 在模板字符串中，空格、缩进、换行都会被保留：
	- ```
	  let message = `
	  	<ul>
	  		<li>1</li>
	  		<li>2</li>
	  	</ul>
	  `;
	  console.log(message);
	  ```
- ## 嵌入变量
	- 模板字符串支持**嵌入变量**，只需要将变量名写在 ${} 之中，其实不止变量，[[#blue]]==任意的 JavaScript 表达式==都是可以的：
	- ```
	  let x = 1, y = 2;
	  let message = `<ul><li>${x}</li><li>${x + y}</li></ul>`;
	  console.log(message); // <ul><li>1</li><li>3</li></ul>
	  ```
	-