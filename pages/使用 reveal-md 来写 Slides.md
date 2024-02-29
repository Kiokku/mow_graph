- >> https://note.isshikih.top/others/reveal-md2Slides/
- >> `reveal-md` 是`reveal.js` 的一个扩展工具，类似于一个预编译工具。利用 `reveal-md`，你可以通过编写一个 `Markdown` 文件来快速简便地生成一个基于网页的 Slides，你也可以将它导出为静态资源并部署在类似 github pages 等平台来分享静态页面。
- [reveal.js 的官方 demo](https://revealjs.com/?demo)
- [reveal-md 的 github repo](https://github.com/webpro/reveal-md)
-
- ## 安装与演示
	- `npm install -g reveal-md`
	- ### 核心命令：
		- ```js
		  ## 最基础的命令，根据 Markdown 文件在本地 1948 端口生成 Slides 服务。
		  $ reveal-md your-md-file.md
		  
		  ## 为了实现在线部署，需要导出为静态资源。
		  $ reveal-md your-md-file.md --static your-static-dir
		  ```
	- ### 操作：
		- 通过点击/ 空格来选择下一页，使用 `h/←` `j/↓` `k/↑` `l/→` 或者点击右下角的方向盘来进行上下左右切换，直接点击页面底部的进度条也可以实现页面跳转。