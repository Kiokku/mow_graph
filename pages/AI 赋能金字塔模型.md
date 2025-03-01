## 确定应用场景
	- 本地部署[工作流程的金字塔模型](https://github.com/AI-FE/pyramid_model)，输入提示词拆封工作流程
	- > **System Prompt** 
	  >软件开发，包括首次开发部署上线以及持续迭代
	- 从 金字塔模型 可以看到，有三个地方会消耗时间：
		- 1. 拆解工作流程
			- 需求、设计、测试评审
			- 系统设计、编写技术文档
		- 2. 完成单个模块
			- 完成开发
			- 完成测试
			- 完成代码部署上线
		- 3. 模块之间的交互
			- 前端与后端联调
			- 前端与产品沟通需求
			- 前端与 UI 沟通设计实现
			- 前端与测试沟通测试细节
	- > **选择原则**
	  > 优先从 提效最明显 的环节下手
- ## 确定 工作模式
	- 两种工作模式：[[#green]]==agent== 与 [[#green]]==workflow==
	  id:: 67c296db-2689-454f-bd5f-87d3be88ddee
		- `workflow`：遵循固定的处理流程，步骤是预先确定的
			- v0
			- Cursor Chat、Cursor Composer Mode
		- `agent`：根据任务动态由大模型自主决定下一步行动，具有更强的自主性和适应性
			- Cursor Agent Mode
			- Devin
- ## 大模型的三重约束
	- 制约 AI 应用能力的三重约束：
		- 模型理解能力
		  logseq.order-list-type:: number
		- [[#red]]==**模型上下文长度**==
		  logseq.order-list-type:: number
		- 模型的私域知识储备
		  logseq.order-list-type:: number
	- 对于 [[#blue]]==软件开发== 场景：
		- 业务代码 = 私域知识，业务代码量增加 -> 私域知识增加
		  logseq.order-list-type:: number
		- 不管是 RAG 还是其他方式，私域知识会占据上下文长度
		  logseq.order-list-type:: number
		- 上下文长度越接近理论极限，理解能力衰减越快
		  logseq.order-list-type:: number
- ## 总结：AI 提效工作流程 决策步骤
	- 工作流程按照 金字塔模型 拆分
	  logseq.order-list-type:: number
	- 选择金字塔中 性价比最高的环节 作为应用场景，确定工作模式
	  logseq.order-list-type:: number
	- 在 AI 三重约束 下持续迭代
	  logseq.order-list-type:: number