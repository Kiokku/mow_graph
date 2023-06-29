- > https://github.com/mqyqingfeng/Blog/issues/108
-
- ## 前言
	- 本篇我们重点介绍以下四种模块加载规范：
		- 1. AMD
		- 2. CMD
		- 3. CommonJS
		- 4. ES6 模块
	- 最后再延伸讲下 Babel 的编译和 webpack 的打包原理。
- ## require.js
	- 在了解 AMD 规范之前，我们先来看看 require.js 的使用方式。
	- 项目目录为：
	- ```
	  * project/
	      * index.html
	      * vender/
	          * main.js
	          * require.js
	          * add.js
	          * square.js
	          * multiply.js
	  ```
	- `index.html` 的内容如下：
	- ```
	  <!DOCTYPE html>
	  <html>
	      <head>
	          <title>require.js</title>
	      </head>
	      <body>
	          <h1>Content</h1>
	          <script data-main="vender/main" src="vender/require.js"></script>
	      </body>
	  </html>
	  ```
	- `data-main="vender/main"` 表示主模块是 `vender` 下的 `main.js`。
	- `main.js` 的配置如下：
	- ```
	  // main.js
	  require(['./add', './square'], function(addModule, squareModule) {
	      console.log(addModule.add(1, 1))
	      console.log(squareModule.square(3))
	  });
	  ```
	- require 的第一个参数表示依赖的模块的路径，第二个参数表示此模块的内容。
	- 由此可以看出，`主模块`依赖 `add 模块`和 `square 模块`。
	- requirejs 项目 Demo 地址：[https://github.com/mqyqingfeng/Blog/tree/master/demos/ES6/module/requirejs](https://github.com/mqyqingfeng/Blog/tree/master/demos/ES6/module/requirejs)
- ## AMD
	- The Asynchronous Module Definition (AMD) 规范。
	- **AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。**
- ## sea.js
	- 文件目录与 requirejs 项目目录相同:
	- ```
	  * project/
	      * index.html
	      * vender/
	          * main.js
	          * require.js
	          * add.js
	          * square.js
	          * multiply.js
	  ```
	- `index.html` 的内容如下：
	- ```
	  <!DOCTYPE html>
	  <html>
	  <head>
	      <title>sea.js</title>
	  </head>
	  <body>
	      <h1>Content</h1>
	      <script src="vender/sea.js"></script>
	      <script>
	      // 在页面中加载主模块
	      seajs.use("./vender/main");
	      </script>
	  </body>
	  
	  </html>
	  ```
	- main.js 的内容如下：
	- ```
	  // main.js
	  define(function(require, exports, module) {
	      var addModule = require('./add');
	      console.log(addModule.add(1, 1))
	  
	      var squareModule = require('./square');
	      console.log(squareModule.square(3))
	  });
	  ```
	- 。。。
	- 跟第一个例子是同样的依赖结构，即 main 依赖 add 和 square，square 又依赖 multiply。
	- seajs 项目 Demo 地址：[https://github.com/mqyqingfeng/Blog/tree/master/demos/ES6/module/seajs](https://github.com/mqyqingfeng/Blog/tree/master/demos/ES6/module/seajs)
	- 而如果我们在浏览器中打开 `index.html`，打印的顺序为：
	- ```
	  加载了 add 模块
	  2
	  加载了 square 模块
	  加载了 multiply 模块
	  9
	  ```
- ## CMD
	- 与 AMD 一样，CMD 其实就是 SeaJS 在推广过程中对模块定义的规范化产出。
- ## AMD 与 CMD 的区别
	- 1.CMD 推崇**依赖就近**，AMD 推崇**依赖前置**。看两个项目中的 main.js：
		- ```
		  // require.js 例子中的 main.js
		  // 依赖必须一开始就写好
		  require(['./add', './square'], function(addModule, squareModule) {
		      console.log(addModule.add(1, 1))
		      console.log(squareModule.square(3))
		  });
		  
		  // sea.js 例子中的 main.js
		  define(function(require, exports, module) {
		      var addModule = require('./add');
		      console.log(addModule.add(1, 1))
		  
		      // 依赖可以就近书写
		      var squareModule = require('./square');
		      console.log(squareModule.square(3))
		  });
		  ```
	- 2.对于依赖的模块，AMD 是**提前执行**，CMD 是**延迟执行**。看两个项目中的打印顺序：
		- AMD 是将需要使用的模块先加载完再执行代码，而 CMD 是在 require 的时候才去加载模块文件，加载完再接着执行。
- ## CommonJS
	- AMD 和 CMD 都是用于**浏览器端**的模块规范，而在**服务器端**比如 node，采用的则是 CommonJS 规范。
	- 导出模块的方式：
		- ```
		  var add = function(x, y) {　
		      return x + y;
		  };
		  
		  module.exports.add = add;
		  ```
	- 引入模块的方式：
		- ```
		  var add = require('./add.js');
		  console.log(add.add(1, 1));
		  ```
	- [[#green]]==跟 sea.js 一致，也是在 require 的时候才去加载模块文件，加载完再接着执行。==
- ## CommonJS 与 AMD
	- [《JavaScript 标准参考教程（alpha）》](http://javascript.ruanyifeng.com/nodejs/module.html):
		- > CommonJS 规范加载模块是**同步**的，也就是说，只有加载完成，才能执行后面的操作。
		  >
		  > AMD规范则是非同步加载模块，允许指定回调函数。
		  >
		  > 由于 Node.js 主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以 CommonJS 规范比较适用。
		  >
		  > 但是，如果是浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此[[#green]]==浏览器端一般采用 AMD 规范==。
- ## ES6
	- ECMAScript2015 规定了新的模块加载方案。
	- 导出模块的方式：
		- ```
		  var firstName = 'Michael';
		  var lastName = 'Jackson';
		  var year = 1958;
		  
		  export {firstName, lastName, year};
		  ```
	- 引入模块的方式：
		- ```
		  import {firstName, lastName, year} from './profile';
		  ```
	-