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
- ## 在服务器上渲染 React 组件
	- Webpack 配置就位后，就可以在服务器上渲染 React 组件了。首先，通过运行以下命令构建服务器端包：
		- ```
		  npm run build:server
		  ```
	- 此命令在 `build` 文件夹中生成一个 `server.js` 文件。
	- 接下来，更新 `server/index.js` 文件以呈现 `AppServer` 组件：
		- ```js
		  / server/index.js
		  const express = require('express');
		  const React = require('react');
		  const ReactDOMServer = require('react-dom/server');
		  const AppServer = require('../src/AppServer').default;
		  
		  const app = express();
		  const PORT = process.env.PORT || 3001;
		  
		  app.get('/', (req, res) => {
		    const content = ReactDOMServer.renderToString(<AppServer />);
		    const html = `
		      <!DOCTYPE html>
		      <html lang="en">
		        <head>
		          <meta charset="UTF-8" />
		          <meta name="viewport" content="width=device-width, initial-scale=1.0" />
		          <title>React SSR</title>
		        </head>
		        <body>
		          <div id="root">${content}</div>
		        </body>
		      </html>
		    `;
		  
		    res.send(html);
		  });
		  
		  app.listen(PORT, () => {
		    console.log(`Server is listening on port ${PORT}`);
		  });
		  ```
	- 在这个文件中，我们导入 `AppServer` 组件，使用 `ReactDOMServer.renderToString()` 将其转换为HTML字符串，并将生成的HTML发送到客户端。
- ## 运行应用程序
	- 现在我们的服务器已经设置完毕，我们可以运行该应用程序了。将新脚本添加到您的 `package.json` 文件中以启动服务器：
		- ```js
		  "scripts": {
		    ...
		    "start:server": "node build/server.js"
		  }
		  ```
	- 要启动服务器，请运行以下命令：
		- ```js
		  npm run start:server
		  ```
	- 打开浏览器并导航至 `http://localhost:3001` 。您应该看到“Hello from Server-Side Rendered React App!”信息。
- ## Hydrating
	- 为了改善用户体验，我们可以使用客户端 React “水合”服务器渲染的 HTML。这允许客户端接管并启用交互性。
	- 首先，更新 `src/AppServer.js` 文件以包含交互式元素，例如按钮：
		- ```js
		  // src/AppServer.js
		  import React, { useState } from 'react';
		  
		  const AppServer = () => {
		    const [count, setCount] = useState(0);
		  
		    return (
		      <div>
		        <h1>Hello from Server-Side Rendered React App!</h1>
		        <p>Counter: {count}</p>
		        <button onClick={() => setCount(count + 1)}>Increment</button>
		      </div>
		    );
		  };
		  
		  export default AppServer;
		  ```
	- 接下来，更新 `src/index.js` 文件以使用 `ReactDOM.hydrate()` 方法：
		- ```js
		  // src/index.js
		  import React from 'react';
		  import ReactDOM from 'react-dom';
		  import AppServer from './AppServer';
		  
		  ReactDOM.hydrate(<AppServer />, document.getElementById('root'));
		  ```
	- 最后，更新 `server/index.js` 文件以服务客户端包：
		- ```js
		  // server/index.js
		  const path = require('path');
		  // ...
		  
		  app.use(express.static(path.resolve(__dirname, '../build')));
		  
		  // ...
		  ```
	- 将新脚本添加到您的 `package.json` 文件中以构建客户端包：
		- ```js
		  "scripts": {
		    ...
		    "build:client": "npm run build"
		  }
		  ```
	- 现在，构建客户端和服务器端包：
		- ```js
		  npm run build:client
		  npm run build:server
		  ```
	- 启动服务器：
		- ```
		  npm run start:server
		  
		  ```
	- 在浏览器中导航至 `http://localhost:3001` 。您应该看到服务器渲染的 React 应用程序带有一个功能计数器按钮。
- <blockquote class="twitter-tweet"><p lang="zh" dir="ltr">有没有pdd的兄弟，我刚才买东西的时候觉得这详情页渲染速度也太快了，有没有你们内部的性能优化文章分享，滚动到最下面新的商品也是详情页的图片和数据做了与渲染<br>0. 数据预加载<br>1. 图片和数据都做了预渲染<br>2. SSR+流式渲染<br>优化的真不错啊 <a href="https://t.co/W5QUQjE2Vo">pic.twitter.com/W5QUQjE2Vo</a></p>&mdash; 花果山大圣 (@shengxj1) <a href="https://twitter.com/shengxj1/status/1721094395024609529?ref_src=twsrc%5Etfw">November 5, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
-