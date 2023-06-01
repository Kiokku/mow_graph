- > https://missing-semester-cn.github.io/2020/course-shell/
-
- ## The Shell
	- 它允许你执行程序，输入并获取某种半结构化的输出。
	- [[#blue]]==Bourne Again SHell==, 简称 “**bash**”
	-
	- ### 使用 shell
	  background-color:: blue
		- `echo $PATH`: 当我们执行 `echo` 命令时，shell 了解到需要执行 `echo` 这个程序，随后它便会在 `$PATH` 中搜索由 `:` 所分割的一系列目录，基于名字搜索该程序。当找到该程序时便执行（假定该文件是 *可执行程序*，后续课程将详细讲解）。我们也可以绕过 `$PATH`，通过直接指定需要执行的程序的路径来执行该程序。
		- `which $PATH`: 确定某个程序名代表的是哪个具体的程序，可以使用 `which` 程序。
	- ### 在shell中导航
	  background-color:: blue
		- `pwd`: 当前工作目录
		- `cd`: 切换目录需要使用 `cd` 命令。在路径中，`.` 表示的是当前目录，而 `..` 表示上级目录
		- `ls`: 查看指定目录下包含哪些文件
		-