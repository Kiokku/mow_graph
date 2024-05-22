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
	- 但是，`git merge -X ours <branch_name>`只适合两边都有修改的情况，在<rename/delete>、<modify/delete>的情况下依然会有冲突需要手动解决；
- ## 3. gitignore
  background-color:: purple
	- 在两个分支添加 `.gitignore`让 git 不跟踪分支上不需要合并的文件夹，然后用`git rm -r --cached <filepath>`取消已跟踪的文件，然后再将 `master`合并到`test`上来；
	- 合并完成后记得去掉 `.gitignore`，然后重新提交之前取消跟踪的文件
- ## 4. git checkout -b <new_branch>
  background-color:: blue
	- 其实最简单的方法就是从`master`拉取一个新分支，对新分支替换`test`上修改的入口程序的build包，其他的不变即可。
	- 当然这种方法就会丢失掉`test`上的提交comment