## 背景
background-color:: pink
	- 公司整个前端的所有 build 包维护在一个仓库中，分支名为 `master`，年初在对管理系统主入口的项目 `sinomes-adm`进行重构、升级到antd 5.x，为了测试部署新环境，build仓库从 `master`拉了一个新分支 `test`;
	- 重构工作间断了一段时间，最近才完成对入口项目的升级。期间 build 仓库 `master`上提交了大量的代码，要把这些提交都 `merge` 到 `test`上来。
	- `├─regexpu-core
	  ├─regexpu-core
	  ├─regexpu-core
	  ├─regexpu-core
	  ├─regexpu-core
	  ...
	- ```
	  ```