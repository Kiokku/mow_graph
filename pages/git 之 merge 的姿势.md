## 背景
background-color:: pink
	- 公司整个前端的所有 build 包维护在一个仓库中，分支名为 `master`，年初在对管理系统主入口的项目 `sinomes-adm`进行重构、升级到antd 5.x，为了测试部署新环境，build仓库从 `master`拉了一个新分支 `test`;
	- 重构工作间断了一段时间，最近才完成对入口项目的升级。期间 build 仓库 `master`上提交了大量的代码，要把这些提交都 `merge` 到 `test`上来。
	- ```
	  ├─sinomes-adm/
	  ├─sinomes-main/
	  ├─sinomes-others
	  ...
	  ```
- ## 1. git merge
  background-color:: gray
	- 最直接的就是 `git merge <branch_name>`.
	- 但是，由于时间间隔比较久，两个分支之间的差异已经相当大（build 仓库中是前端项目打包后bundle文件，每次提交都会有相当大差异），直接用 `git merge`有相当多的冲突。
- ## 2. git merge --strategy 和 git merge --strategy-options
  background-color:: gray
	- 采用 `git`的合并策略： [merge strategy](https://blog.walterlv.com/post/git-merge-strategy.html)
	- 但是，`git merge -X ours <branch_name>`只适合两边都有