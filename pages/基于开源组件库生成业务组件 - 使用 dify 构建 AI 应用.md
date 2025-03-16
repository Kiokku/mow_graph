## Dify
	- ### [Dify](https://dify.ai/)
		- 聊天助手
		- Agent
		- Workflow
	- ### 初始化配置
		- dify 设置 -> 模型供应商 -> 添加更多模型供应商 -> OpenAI-API-compatible -> 添加 302 的 API key
- ## 1、用专业的知识明确需求
  background-color:: red
	- ### 业务组件使用的技术栈
		- `React`
		- `TailwindCSS`
		- `antd`
	- ### 前后端状态分离
		- 所有需要请求服务端数据的操作，都通过 props 暴露给外部的页面来进行对接联调
	- ### 统一的文件结构和代码规范
		- ```
		  app/components/BizComponentExample
		  ├─ index.ts // 仅仅将组件内容暴露给外部
		  ├─ interface.ts // 定义组件内部用到的所有类型，包括 interface、type、enum 等
		  ├─ BizComponentExample.stories.tsx // 组件的 storybook 文档，包含组件不同的使用示例
		  ├─ BizComponentExample.tsx // 组件的主体样式和主体逻辑，如果组件太大(超过 500 行)可以拆分为其它的文件，样式使用 tailwindcss 编写
		  ├─ helpers.ts // 组件所有的工具函数存放在此 (如有)
		  ```
- ## 2、选择合适的模型
  background-color:: red
- ## 3、运用提示词技巧编写提示词
  background-color:: red
	-