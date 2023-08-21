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
		- `webpack serve --open`：cli 启动dev-server
		- `contentBase`：指定[[#green]]==根目录==或者用`static`指定[[#green]]==静态资源==
	- ### 处理 ES6
	  background-color:: pink
		- `babel-loader`
		- `.babelrc`
	- ### 处理 CSS
	  background-color:: pink
		- `style-loader`
		- `css-loader`
		- `postcss-loader`：给 CSS 自动加前缀、使用下一代 CSS 语法
	-
- ## Webpack 高级配置
	- ### webpack 如何配置多入口
	  background-color:: pink
		- `entry: './app/entry'`：只有1个入口，入口只有1个文件
		- `entry: ['./app/entry1', './app/entry2']`： 只有1个入口，入口有2个文件
		- `entry: { a: './app/entry-a', b: ['./app/entry-b1', './app/entry-b2']}`：有2个入口
		- 对应输出output：
			- ```
			  output: {
			    // 输出文件的名称
			    filename: 'bundle.js', // 完整的名称
			    filename: '[name].js', // 当配置了多个 entry 时，通过名称模版为不同的 entry 生成不同的文件名称
			    filename: '[chunkhash].js', // 根据文件内容 hash 值生成文件名称，用于浏览器长时间缓存文件
			  ```
		- 多入口生成html：
			- ```
			  const { WebPlugin } = require('web-webpack-plugin');
			  
			  new WebPlugin({
			  	filename: 'index.html',
			  	// html template file path（full path relative to webpack.config.js）
			  	template: './template.html',
			  	requires: ['A', 'B'],
			  }),
			  ```
	- ### webpack 抽离CSS 文件
	  background-color:: pink
		- dev环境：配置不变，`style-loader`、`css-loader`、`postcss-loader`
		- 生产环境：
			- `MiniCssExtractPlugin.loader`：不再使用style- loader；
			- `css-loader`
			- postcss-