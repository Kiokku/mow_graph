- > https://github.com/mqyqingfeng/Blog/issues/101
-
- ## 前言
	- 我们以`查找指定目录下的最大文件`为例，感受从
	- 回调函数 -> Promise -> Generator -> Async
	- 异步处理方式的改变。
- ## API 介绍
	- 为了实现这个功能，我们需要用到几个 Nodejs 的 API，所以我们来简单介绍一下。
	- ### fs.readdir
	  background-color:: green
		- readdir 方法用于读取目录，返回一个包含文件和目录的数组。
	- ### fs.stat
	  background-color:: green
		- stat 方法的参数是一个文件或目录，它产生一个对象，该对象包含了该文件或目录的具体信息。此外，该对象还有一个 isFile() 方法可以判断正在处理的到底是一个文件，还是一个目录。
- ## 思路分析
	-