- > 基于开源知识库平台快速使用 RAG - `Dify`
- ## 如何准备私有组件库的 RAG 知识库数据
	- ### 关键点
	  background-color:: green
		- 组件 Chunk 知识的`完整性`保证
		  logseq.order-list-type:: number
		- chunk 包含的组件的语义和功能是`清晰`的（因为向量的检索是根据语义相似度来检索的）
		  logseq.order-list-type:: number
	- ### 组件 Chunk 知识完整性保证
		- 将单个私有组件的知识库数据放在单独的 md 文件中保存，每个 md 文件内容就是单个的 Chunk，如下：
			- ```markdown
			  table.md
			  
			  <!-- 这里是Table组件的知识库数据 -->
			  ```
	- ### Chunk 包含的组件的语义和功能清晰性保证
		- 在知识库数据中，可以包含组件的`功能描述`、`使用场景`、`props类型定义`、`代码示例`等信息
		- > [[#red]]==Q: 直接把组件的完整代码放进去是否可以？==
		- > A：[[#green]]==不建议==，全量代码占用的上下文太多，尽管现阶段的 AI 已经支持了超大的长下文 Context，但是随着 Context 的长度越大，AI 的推理能力也会下降，容易抓不到问题的`重点`。
		- 举例：
		- ```md
		  # Table
		  
		  ## How To Use（使用场景）
		  
		  Table 组件用于展示数据，通常用于展示列表数据。
		  
		  ## API（组件的 API）
		  
		  - data: Array<{ name: string, age: number }>
		  - columns: Array<{ title: string, dataIndex: string }>
		  ```
		- > 可以参考 **Antd** 的组件库文档编写规范，基本上直接可以拿过来作为 RAG 的知识库数据
	-