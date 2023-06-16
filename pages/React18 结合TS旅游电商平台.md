- > https://coding.imooc.com/learn/list/475.html
-
- ## 项目规划(需求分析)
  collapsed:: true
	- [[#blue]]==业务规划图==
		- ![image.png](../assets/image_1684418859848_0.png)
	- [[#blue]]==页面规划==
		- `产品模块`
			- ![image.png](../assets/image_1684419068793_0.png)
		- `用户模块`
			- ![image.png](../assets/image_1684419257134_0.png)
		- `购物模块`
			- ![image.png](../assets/image_1684419284978_0.png)
		- `订单模块`
			- ![image.png](../assets/image_1684419310605_0.png)
			-
- ## 系统设计与项目初始化
	- [[#blue]]==界面设计==
		- **UI设计规范**
	- [[#blue]]==需求设计==
		- **需求设计规范 (SRS: Software Requirement Specific）**
	- [[#blue]]==项目初始化==
		- `框架`: [[$blue]]==create-react-app==
		- `项目依赖及配置文件`：
			- `npm install typescript-plugin-css-modules --save-dev`
			- `tsconfig.json` 加载css-modules插件plugins
			- ` .vscode` typescript sdk
			- `custom.d.ts` css module
- ## 主页开发
  collapsed:: true
	- [[#blue]]==文件夹结构==
		- ![image.png](../assets/image_1686211132512_0.png){:height 395, :width 257}
		- `index.ts`：导出组件，避免过长的组件引用地址
			- ```
			  import { Header, Footer } from "./components";
			  ```
	-
- ## 路由系统
	- ### 路由与SPA
	  background-color:: pink
		- `SPA`：单页网站应用
			- JS、HTML、CSS文件打包为一个文件，一次性丢给浏览器。
			- JS劫持浏览器路由，生成[[#green]]==虚拟路由==动态渲染页面dom元素。
	- ### React路由解决方案
	  background-color:: pink
		- [[#blue]]==`react-router`-v5==
			- `安装`：`yarn add react-router-dom`
			- `react-router-dom`：用于浏览器，处理Web App的路由。
			- `react-router-native`：用于React Native，处理手机App的路由。
			- `react-router-redux`：