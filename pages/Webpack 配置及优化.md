- ## Webpack基础
	- ### 拆分配置和 merge
	  background-color:: pink
		- 不同环境对应不同的webpack配置文件**拆分**：
			- - webpack.common.js 通用配置
			- - webpack.dev.js 只在dev开发环境配置，如dev-server
			- - webpack.prod.js 在生成环境的配置，如contentHash、图片引入base64
		- **merge**：
			- ```
			  const { smart } = require('webpack-merge')；
			  const webpackCommonConf = require('./webpack.common.js');
			  
			  module.exports = smart(webpackCommonConf, {
			      ....
			  })
			  ```
	- ### 启动本地服务 dev-server
	  background-color:: pink
		-
		-