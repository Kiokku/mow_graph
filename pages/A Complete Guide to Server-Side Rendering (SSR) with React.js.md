- > https://codedamn.com/news/reactjs/server-side-rendering-reactjs
-
- ## 什么是服务器端渲染 (SSR)？
	- 服务器端渲染（SSR）是在服务器而不是客户端（浏览器）上渲染网页的过程。在使用 React.js 构建的典型单页应用程序 (SPA) 中，浏览器接收一个空的 HTML 文档，React 在客户端渲染组件。通过 SSR，服务器生成 HTML 并将其发送到浏览器，浏览器立即显示内容。
	- ### SSR 的好处
		- 缩短初始加载时间：通过 SSR，用户可以立即看到内容，从而提高应用程序的感知性能。
		  logseq.order-list-type:: number
		- 更好的 SEO：搜索引擎爬虫可以更轻松地索引服务器呈现的页面，从而提高网站的搜索引擎优化 (SEO)。
		  logseq.order-list-type:: number
		- 性能更可靠：由于服务器处理渲染，因此设备速度较慢或网络条件较差的用户仍然会收到完全渲染的页面。
		  logseq.order-list-type:: number
- ## 先决条件
	- 对 React.js 和 Node.js 有基本了解。
	  logseq.order-list-type:: number
	- Node.js 安装在本地计算机上。
	  logseq.order-list-type:: number
	- 代码编辑器（例如 Visual Studio Code）。
	  logseq.order-list-type:: number
- ## 设置项目
	- ```commonlisp
	  npx create-react-app ssr-react-app
	  cd ssr-react-app
	  ```
- ## 添加 Express 服务器
	- 首先，安装必要的依赖项：
		- ```asterisk
		  npm install express
		  ```
	- 在项目的根目录中创建一个名为 `server` 的新文件夹，并添加一个名为 `index.js` 的新文件。该文件将包含 Express 服务器配置。
		- ```js
		  // server/index.js
		  const express = require('express');
		  const app = express();
		  const PORT = process.env.PORT || 3001;
		  
		  app.listen(PORT, () => {
		    console.log(`Server is listening on port ${PORT}`);
		  });
		  ```
- ## 为 SSR 构建 React 组件
	- 在 `src` 文件夹中，创建一个名为 `AppServer.js` 的新文件。该组件将是我们应用程序的服务器渲染版本。
		- ```js
		  // src/AppServer.js
		  import React from 'react';
		  
		  const AppServer = () => {
		    return (
		      <div>
		        <h1>Hello from Server-Side Rendered React App!</h1>
		      </div>
		    );
		  };
		  
		  export default AppServer;
		  ```
- ## 为 SSR 设置 Webpack
	- 要使用服务器端渲染，我们需要使用 Webpack 打包 React 应用程序。首先，让我们安装所需的依赖项：
		- ```
		  npm install webpack webpack-cli webpack-node-externals @babel/core @babel/preset-env @babel/preset-react babel-loader --save-dev
		  ```
	- 接下来，在项目的根目录下创建一个名为 `webpack.server.js` 的新文件。该文件将包含用于服务器端渲染的 Webpack 配置。
		- ```js
		  // webpack.server.js
		  const path = require('path');
		  const nodeExternals = require('webpack-node-externals');
		  
		  module.exports = {
		    target: 'node',
		    mode: 'development',
		    externals: [nodeExternals()],
		    entry: './server/index.js',
		    output: {
		      filename: 'server.js',
		      path: path.resolve(__dirname, 'build'),
		    },
		    module: {
		      rules: [
		        {
		          test: /\.(js|jsx)$/,
		          exclude: /node_modules/,
		          use: {
		            loader: 'babel-loader',
		            options: {
		              presets: [
		                '@babel/preset-env',
		                '@babel/preset-react',
		              ],
		            },
		          },
		        },
		      ],
		    },
		    resolve: {
		      extensions: ['.js', '.jsx'],
		    },
		  };
		  ```
	- 将新脚本添加到您的 `package.json` 文件中以构建服务器端包：
		- ```js
		  "scripts": {
		    ...
		    "build:server": "webpack --config webpack.server.js"
		  }
		  ```
	-