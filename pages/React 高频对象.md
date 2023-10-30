- > 在 React 应用中, 有很多特定的对象或数据结构. 了解这些内部的设计, 可以更容易理解 react 运行原理. 本章主要列举从 react 启动到渲染过程出现频率较高, 影响范围较大的对象, 它们贯穿整个 react 运行时.
- ## react 包
	- 此包定义 react 组件(`ReactElement`)的必要函数, 提供一些操作`ReactElement`对象的 api.
	- 所以这个包的核心需要理解`ReactElement`对象, 假设有如下入口函数:
		- ```
		  // 入口函数
		  ReactDOM.render(<App />, document.getElementById('root'));
		  ```
	- 可以简单的认为, 包括`<App/>`及其所有子节点都是`ReactElement`对象(在 render 之后才会生成子节点, 后文详细解读), [[#green]]==每个`ReactElement`对象的区别在于 type 不同.==
	- ### [ReactElement 对象](https://github.com/facebook/react/blob/v17.0.2/packages/react/src/ReactElement.js#L126-L146)
	  background-color:: pink
		- > *其 type 定义在*[`shared`包中](https://github.com/facebook/react/blob/v17.0.2/packages/shared/ReactElementType.js#L15)*.*
		- 所有采用`jsx`语法书写的节点, 都会被编译器转换, 最终会以`React.createElement(...)`的方式, 创建出来一个与之对应的`ReactElement`对象.
		- `ReactElement`对象的数据结构如下:
		- ```
		  export type ReactElement = {|
		    // 用于辨别ReactElement对象
		    $typeof: any,
		  
		    // 内部属性
		    type: any, // 表明其种类
		    key: any,
		    ref: any,
		    props: any,
		  
		    // ReactFiber 记录创建本对象的Fiber节点, 还未与Fiber树关联之前, 该属性为null
		    _owner: any,
		  
		    // __DEV__ dev环境下的一些额外信息, 如文件路径, 文件名, 行列信息等
		    _store: {validated: boolean, ...},
		    _self: React$Element<any>,
		    _shadowChildren: any,
		    _source: Source,
		  |};
		  ```
		- 需要特别注意 2 个属性:
			- `key`属性在`reconciler`阶段会用到, 目前只需要知道所有的`ReactElement`对象都有 key 属性(且[其默认值是 null](https://github.com/facebook/react/blob/v17.0.2/packages/react/src/ReactElement.js#L348-L357), 这点十分重要, 在 diff 算法中会使用到).
			  logseq.order-list-type:: number
			- `type`属性决定了节点的种类:
			  logseq.order-list-type:: number
				- 它的值可以是字符串(代表`div,span`等 dom 节点), 函数(代表`function, class`等节点), 或者 react 内部定义的节点类型(`portal,context,fragment`等)；
				  logseq.order-list-type:: number
				- 在`reconciler`阶段, 会根据 type 执行不同的逻辑；
				  logseq.order-list-type:: number
	- ### [ReactComponent](https://github.com/facebook/react/blob/v17.0.2/packages/react/src/ReactBaseClasses.js#L20-L30) 对象
	  background-color:: pink
		-