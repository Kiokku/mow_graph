## 背景
	- 目前代码的git仓库部署在内网沙盒环境中，需要将所有仓库的代码（包括所有的分支）迁移到外网新搭建的git服务器；
- ## git clone --mirror
	- 获得仓库镜像
	  logseq.order-list-type:: number
	  collapsed:: true
		- ```
		  # 进入工作目录
		  mkdir git_migration && cd git_migration
		  
		  # 克隆所有分支和标签的镜像仓库（保留完整历史）
		  git clone --mirror <旧仓库内网URL>
		  
		  # 进入克隆的仓库目录
		  cd <仓库名.git>
		  
		  # 如果仓库包含 LFS 文件（可选）
		  git lfs fetch --all  # 确保拉取所有 LFS 对象
		  ```
	- 打包仓库镜像
	  logseq.order-list-type:: number
		- ```
		  # 返回上级目录
		  cd ..
		  
		  # 压缩仓库（保留 .git 目录）
		  tar czvf <仓库名>.tar.gz <仓库名.git>
		  ```
	- 通过介质传输到外网
	  logseq.order-list-type:: number
	- logseq.order-list-type:: number