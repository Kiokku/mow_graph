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
	-
	- ``````