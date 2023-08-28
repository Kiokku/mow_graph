- ## Webpack基础
  collapsed:: true
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
  collapsed:: true
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
			- `MiniCssExtractPlugin.loader`：不再使用style- loader，抽离css到单独文件；
			- `css-loader`
			- `postcss-loader`
			- [[#green]]==抽离文件并压缩==：
				- ```
				  plugins: [
				  	new MiniCssExtractPlugin({
				      	filename: 'css/main.[contentHash:8].css'
				      })
				  ];
				  
				  optimization: [
				  	minimizer: [
				      	new CssMinimizerPlugin(),
				          // or 
				          // new TerserPlugin(), new OptimizeCssAssetsPlugin()
				      ]
				  ]
				  ```
	- ### webpack 抽离公共代码
	  background-color:: pink
		- 公共代码包括：**公共的引用**和**第三方依赖包**。
		- webpack 4.0 之前的方案：[[#green]]==CommonsChunkPlugin==；4.0 之后的方案：[[#green]]==optimization.splitChunks==
		- optimization
			- splitChunks
				- chunks: `all`、`initial`、`async`
				- cacheGroup 缓存分组
					- vendor 第三方模块
						- name: 'vendor',  // chunk 名称
						- priority: 1, // 权限更高，优先抽离，重要！！
						- test: \/node_modules\/,
						- miniSize: 0, // 大小限制
						- minChunks: 1, // 最少复用次数
					- common 公共模块
						- name: 'common',
						- priority: 0,
						- miniSize: 0,
						- minChunks: 2,
	- ### webpack 处理JSX
	  background-color:: pink
		- `.babelrc`
		- ```
		  {
		  	"presets": ["@babel/preset-react"]
		  }
		  ```
	- ### module chunk bundle 的区别
	  background-color:: pink
		- `module`：各个源码文件，webpack 中一切皆模块；
		- `chunk`：多模块合并的代码块，如 entry、import()、splitChunk；
		- `bundle`：最终输出打包文件
- ## Webpack 性能优化
	- ### 构建速度优化
	  background-color:: pink
		- [[#green]]==优化 babel-loader==
			- ```
			  {
			  	test: /.js$/,
			      use: ['babel-loader?cacheDirectory'], // 开启缓存
			      include: path.resolve(_dirname, 'src'), // 明确作用范围
			      // or exclude
			  }
			  ```
		- [[#green]]==IgnorePlugin 避免引入无用模块==
			- `new webpack.IgnorePlugin(/\.\/locale/,  /moment/)`
		- [[#green]]==noParse 避免重复打包==
			- 类似 xxxx.min.js 已经压缩打包的，避免重复打包
			- ```
			  module: {
			  	// 忽略对'react.min.js'文件的递归解析处理
			      noParse: [/react\.min\.js$/],
			  }
			  ```
		- [[#blue]]==* happyPack 多进程打包==
			- JS 单线程，开启**多进程**打包，提高构建速度
			- 适合大项目，较多文件时开启；
			- ```
			  const HappyPack = require('happypack');
			  
			  module.export = {
			  	...,
			      module: {
			      	rules: [
			          	// js
			              {
			              	test: /\.js$/,
			                  // 把对.js文件的处理转交给id为babel的HapptPack实例
			                  use: [happypack/loader?id=babel],
			                  include: ...,
			              }
			          ],
			      },
			      plugins: [
			      	// HappyPack 开启多进程
			          new HappyPack({
			          	id: 'babel',
			              loaders: [babel-loader?cacheDirectory]
			          })
			      ]
			  }
			  ```
		- [[#blue]]==ParallelUglifyPlugin 多进程压缩 JS==
			- ```
			  const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin')
			  
			  ...
			  	// 使用 ParallelUglifyPlugin 并行压缩输出的 JS 代码
			          new ParallelUglifyPlugin({
			              // 传递给 UglifyJS 的参数
			              // （还是使用 UglifyJS 压缩，只不过帮助开启了多进程）
			              uglifyJS: {
			                  output: {
			                      beautify: false, // 最紧凑的输出
			                      comments: false, // 删除所有的注释
			                  },
			                  compress: {
			                      // 删除所有的 `console` 语句，可以兼容ie浏览器
			                      drop_console: true,
			                      // 内嵌定义了但是只用到一次的变量
			                      collapse_vars: true,
			                      // 提取出出现多次但是没有定义成变量去引用的静态值
			                      reduce_vars: true,
			                  }
			              }
			          })
			  ```
		- [[#green]]==热更新==
			- [[#red]]==自动更新==：整个网页刷新，速度较慢， 状态会丢失；
			- [[#green]]==**热更新**==：新代码生效，网页不刷新，状态不丢失；
			- ```
			  const HotModuleReplacementPlugin = require('webpack/lib/HotModuleReplacementPlugin');
			  
			  ...
			  	entry: {
			          // index: path.join(srcPath, 'index.js'),
			          index: [
			              'webpack-dev-server/client?http://localhost:8080/',
			              'webpack/hot/dev-server',
			              path.join(srcPath, 'index.js')
			          ],
			          ...
			      },
			      ...
			      plugins: [
			      	...,
			          new HotModuleReplacementPlugin()
			      ],
			      devServer: {
			          port: 8080,
			          progress: true,  // 显示打包的进度条
			          contentBase: distPath,  // 根目录
			          open: true,  // 自动打开浏览器
			          compress: true,  // 启动 gzip 压缩
			  
			          hot: true,
			      },
			      ...
			  ```
		- [[#green]]==DllPlugin 动态链接库插件==
			- webpack 已内置 DllPlugin 支持；
			- DllPlugin - 打包出 dll 文件；
			- DllReferencePlugin - 使用 dll 文件；
			-
			-