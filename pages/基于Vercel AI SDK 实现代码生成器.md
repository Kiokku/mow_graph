## Embedding 实现
	- ### **1、Vercelai 数据表初始化**
	  background-color:: green
		- 复制一份`lib/db/openai`文件夹，重命名为`lib/db/vercelai`。
		- 修改`lib/db/vercelai/schema.ts`文件，将`openai`替换为`vercelai`。
		- 同时修改`lib/db/vercelai/actions.ts`、`lib/db/vercelai/selectors.ts`中对应的引用。
		- 执行数据库同步命令：
			- ```
			  pnpm db:generate
			  pnpm db:migrate
			  ```
	- ### **2、将基于 openai 的 embedding 逻辑重构为基于 vercelai 的逻辑**
	  background-color:: green
		- 使用 `cursor` 重构 embedding 逻辑时，添加 `@Docs`
			- **llms.txt**：[https://llmstxt.org/](https://llmstxt.org/) - to provide information to help LLMs use a website at inference time
			  logseq.order-list-type:: number
			- **vercelai 的 llms.txt**：[https://sdk.vercel.ai/llms.txt](https://sdk.vercel.ai/llms.txt)
			  logseq.order-list-type:: number
	- ### **3.编写脚本，将私有组件知识库转换为 embeddings，保存到数据库中**
	  background-color:: green
		- `embedDocs.ts`
		- `pnpm run embedDocs:vercelai`
- ## 实现 RAG API 的逻辑
	- `route.ts`重构
	- `types.ts`重构