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
		-