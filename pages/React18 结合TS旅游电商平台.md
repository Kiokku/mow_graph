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
  collapsed:: true
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
  collapsed:: true
	- ### 路由与SPA
	  background-color:: pink
		- `SPA`：单页网站应用
			- JS、HTML、CSS文件打包为一个文件，一次性丢给浏览器。
			- JS劫持浏览器路由，生成[[#green]]==虚拟路由==动态渲染页面dom元素。
	- ### React路由解决方案
	  background-color:: pink
		- #### react-router v5
		  background-color:: green
			- [react-router v5 文档](https://v5.reactrouter.com/)
			- `安装`：`npm i react-router-dom@5.3.4`
			- `react-router-dom`：用于浏览器，处理Web App的路由。
			- `react-router-native`：用于React Native，处理手机App的路由。
			- `react-router-redux`：路由中间件，处理redux集成。
			- `react-router-config`：静态配置路由。
			- > 注意⚠️：react-router没有提供TypeScript支持，需要安装react-router类型定义：`@types/react-router-dom`
			- > ⚠️官方或半官方typing支持：`npm install @types/XXXXXX`
			- > `npm install --save-dev`
			  >> `--save`安装依赖时自动添加版本号信息到package-json.js
			  >
			  >> `-dev`安装依赖到devDependencies
			- `<BrowserRouter />`：路由导航与原生浏览器操作行为一致
			- `<Route />`：路由的路径解析原理与原生浏览器一致，可以自动识别url路径，`component`中传递`history`，`location`，`match`的props
			- `<Switch />`：路由的切换以页面为单位，不要页面堆叠
			- `withRouter`：HOC，向页面子组件传递路由props（history、location、match）
			- `useRouter`：Hook，向页面组件传递路由信息
				- ```
				  const history = useHistory()
				  const location = useLocation()
				  const params = useParams()
				  const match = useRouteMatch()
				  ```
			- `<Link to={} />`：代替history.push()
		- #### react-router v6
		  background-color:: green
			- `<BrowserRouter />` `<Routes />` `<Route />`：v6废除了`<Switch />`，使用这三个组件的固定搭配。
			- [[#red]]==~~`withRouter`~~==：v6 全面倒向**函数式组件**，不再支持类组件。
			- `useRouter`：`useLocation`、[[#red]]==~~`useHistory`~~==、[[#green]]==`useNavigate`==、`useParams`
			- `<Link />`
			- [[#blue]]==支持类组件==：HOC
				- ```
				  export const withRouter = (Component) => {
				      const WrappedComponent = (props) => {
				          const navigate = useNavigate()
				          return (
				              <Component navigate={navigate} {...props} />
				          )
				      }
				      return WrappedComponent
				  }
				  ```
- ## Redux-项目架构方案
	- ![image.png](../assets/image_1687967254772_0.png)
	- ![image.png](../assets/image_1687967510570_0.png)
	- ### state和store
	  background-color:: pink
		- 创建state的数据订阅仓库：`createStore(Reducer)`
		- 获取store数据：`store.getState()`
		- 更新state，action与reducer的处理：
			- `dispatch`：`store.dispatch(action)`
			- `reducer`：`(state, action) => {}`
			- `action`: `{ type: string, payload: object }`
			- state的immutable（不可变性）：在`reducer`中处理state要用newState代替state，[[#red]]==不能直接在state上进行修改==。`const newState = {...state}`
		- 订阅state，store的连接与订阅：`store.subscribe(callback)`
	- ### I18n
	  background-color:: pink
		- 原理：语言包作为静态资源单独保存（xml、json），每种语言对应一个文件，切换语言设置时，语言文件也会随之切换。
		- I18n工具：`i18next`
			- `react- i8next`
		-
		-
	-