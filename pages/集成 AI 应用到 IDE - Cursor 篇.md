## 集成 AI 应用
	- ### 方式一：.cursorrules
	  background-color:: green
		- 在项目根目录下创建 `.cursorrules` 文件（or cursor settings 中配置 Rules for AI）
		  logseq.order-list-type:: number
		- 写入`antd-component-codegen.md` 中的提示词
		  logseq.order-list-type:: number
			- ![image.png](../assets/image_1743088733503_0.png)
		- **提示词调度分析**:
			- 类似于 `Cline` 的`clinerules`调度机制
			- 通过 `system prompt` 注入的方式
	- ### 方式二：Notepad
	  background-color:: green
		- [Cursor Notepad](https://docs.cursor.com/features/beta/notepads#what-should-i-write-in-notepads)
	- ### 方式三：cursorrules + prompt file
	  background-color:: green
	- ### 方式四：.cursor/rules
	  background-color:: green
	- ### 方式五：自定义 Composer Agent Command Tools
	  background-color:: green
		- 修改`dify-mcp-server`，兼容通过 command 执行直接输出`console log`
		  logseq.order-list-type:: number
		- 新增 package.json 中的 script：`npm run biz-component-codegen`
		  logseq.order-list-type:: number
		- 修改`cursorrules`，注入`生成业务组件`场景需要调度执行的 `command tools`
		  logseq.order-list-type:: number
- ## Continue VS Cline VS Cursor
	- ### 场景
		- 场景：基于开源组件库生成业务组件
	- ### 选择标准
		- 使用体验
		- 数据私密性
		- 成本
	- ![image.png](../assets/image_1743518622616_0.png){:height 592, :width 411}
	-