- ## JavaScript 的代码开销和如何缩短解析时间
	- ### 代码开销
	  background-color:: pink
		- 加载
		- 解析和编译 Parse/Compile
		- 执行 Execuiton
	- ### 缩短解析时间方案
	  background-color:: pink
		- #### Code splitting 代码拆分
		- #### Tree shaking 代码减重
		- ### 减少主线程工作量
			- 避免长任务
			- 避免超过 1kb 的行间脚本( inline script)
			- 使用 rAF 和 rIC 进行时间调度
			- Progressive Bootstrapping 渐进式启动首屏
				- ![image.png](../assets/image_1696739703129_0.png)
- ## 配合 V8 引擎，有效优化代码
	- ### V8 编译原理
	  background-color:: pink
		-
	-