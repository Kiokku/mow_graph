## 背景
	- 目前代码的git仓库部署在内网沙盒环境中，需要将所有仓库的代码（包括所有的分支）迁移到外网新搭建的git服务器；
- ## git clone --mirror
	- 获得仓库镜像
	  logseq.order-list-type:: number
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
	- 解压并推送仓库
	  logseq.order-list-type:: number
		- ```
		  # 解压文件
		  tar xzvf <仓库名>.tar.gz
		  
		  # 进入仓库目录
		  cd <仓库名.git>
		  
		  # 更新远程地址为新仓库 URL
		  git remote set-url origin <新仓库外网URL>
		  
		  # 推送所有分支、标签和提交记录
		  git push --mirror origin
		  
		  # 如果含 LFS 文件（可选）
		  git lfs push --all origin
		  ```
- ## 批量迁移脚本
	- #### **导出脚本 (`export_repos.sh`)**
		- ```
		  #!/bin/bash
		  REPOS=("repo1.git" "repo2.git")  # 替换为仓库URL列表
		  
		  for repo in "${REPOS[@]}"; do
		    git clone --mirror $repo
		    tar czvf $(basename $repo).tar.gz $(basename $repo)
		  done
		  ```
	- #### **导入脚本 (`import_repos.sh`)**
		- ```
		  #!/bin/bash
		  GITEA_URL="http://gitea.example.com/username"  # 替换为你的Gitea地址
		  
		  for archive in *.tar.gz; do
		    tar xzvf $archive
		    repo_dir=${archive%.tar.gz}
		    cd $repo_dir
		    git remote set-url origin $GITEA_URL/${repo_dir%.git}.git
		    git push --mirror origin
		    cd ..
		  done
		  ```
- ## 为什么   `--mirror`   足够且更优？
	- **裸仓库包含所有必要数据**：
		- 所有 commit 历史
		- 所有分支指针（`refs/heads/*`）
		- 所有标签（`refs/tags/*`）
		- 所有 Git 对象（blob/tree/commit）
	- **工作文件无需迁移**：
		- 工作区文件是 Git 对象数据库的**派生产物**
		- 开发者在新仓库 `git clone` 时会自动生成工作文件
	- **迁移效率更高**：
		- 裸仓库体积更小（无冗余工作文件）
		- 保留完整的版本控制元数据