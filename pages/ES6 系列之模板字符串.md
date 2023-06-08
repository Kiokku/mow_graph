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
	- ```
	  let arr = [{value: 1}, {value: 2}];
	  let message = `
	  	<ul>
	  		${arr.map((item) => {
	  			return `
	  				<li>${item.value}</li>
	  			`
	  		})}
	  	</ul>
	  `;
	  console.log(message);
	  ```
- ## 标签模板
	- 模板标签是一个非常重要的能力，模板字符串可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串，举个例子：
	- ```
	  let x = 'Hi', y = 'Kevin';
	  var res = message`${x}, I am ${y}`;
	  console.log(res);
	  ```
	- 我们可以自定义 message 函数来处理返回的字符串:
	- ```
	  // literals 文字
	  // 注意在这个例子中 literals 的第一个元素和最后一个元素都是空字符串
	  function message(literals, value1, value2) {
	  	console.log(literals); // [ "", ", I am ", "" ]
	  	console.log(value1); // Hi
	  	console.log(value2); // Kevin
	  }
	  ```
	- 我们利用这些参数将其拼合回去：
	- ```
	  function message(literals, ...values) {
	  	let result = '';
	  
	  	for (let i = 0; i < values.length; i++) {
	  		result += literals[i];
	  		result += values[i];
	  	}
	  
	  	result += literals[literals.length - 1];
	  
	  	return result;
	  }
	  ```
	- 你也可以这样写：
		-