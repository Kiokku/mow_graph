- > 本节从宏观上分析了`reconciler 运作流程`, 并将其分为了 4 个步骤, 基本覆盖了`react-reconciler`包的核心逻辑.
-
- ## 概览
	- `react-reconciler`包的主要作用, 将主要功能分为 4 个方面：
		- **输入**: 暴露`api`函数(如: `scheduleUpdateOnFiber`), 供给其他包(如`react`包)调用.
		  logseq.order-list-type:: number
		- **注册调度任务**: 与调度中心(`scheduler`包)交互, 注册调度任务`task`, 等待任务回调.
		  logseq.order-list-type:: number
		- **执行任务回调**: 在内存中构造出`fiber树`, 同时与与渲染器(`react-dom`)交互, 在内存中创建出与`fiber`对应的`DOM`节点.
		  logseq.order-list-type:: number
		- **输出**: 与渲染器(`react-dom`)交互, 渲染`DOM`节点.
		  logseq.order-list-type:: number
		- ![image.png](../assets/image_1699023235504_0.png)
- ## 分解
	- ### 输入
	  background-color:: pink
		-