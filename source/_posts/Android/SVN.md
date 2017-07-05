---
title: SVN Git 基本
date: 2016-03-31 09:13:12
tags: SVN GIT
category: program

---


# SVN Git

## 软件

svn 服务器，客户端

git


## studio 配置svn

打开Android studio 设置 搜索 `svn`，如果没有安装命令行的svn需要去除一个勾。

<!--more-->

![](http://i.imgur.com/Mf9pWBm.png)

### 如果没有安装svn命令行版
>Cannot load supported formats: Cannot run program "svn": CreateProcess error=2,

原因：是SVN使用了命令行工具，如果本地没有SVN的命令行工具，则导致出错。

需呀进行上面的操作


## 上传文件

不提交的文件：

- build 目录
- local.properties

上传忽略设置：

文件右击--> set properties --> ignore属性 不要设置值

## 工程中颜色
- 绿色：代表增加到服务器，但是还有没提交
- 蓝色：修改了，但是还没有上传
- 白色：单前文件和服务器版本一致
- 红色：文件版本落后于服务器；文件没有加入到版本管理的范畴内


## 完整修改一个文件的流程

1. update file
2. lock-->可以看到lock选项是灰色的
3. 修改
4. commit
5. 提交后会自动解锁

## 加载一个服务器的工程

在studio登入界面 impot 。。。。


## Git


设定用户名密码：

	git config --global user.name "username"
	git config --global user.email "email.."

创建本地仓库：

	git init
	
提交一个本地文件到本地仓库

	git add 。。。
	git commit  
	git commit -m "提交信息"

连接远程仓库：https模式需呀输入密码

	git remote add origin https://github.com/fangmd/movie.git
	git push -u origin master

ssh连接方式：

见 [git安装](http://fangmd.github.io/2016/03/28/program/git%E5%AE%89%E8%A3%85/)


## 将studio 工程提交到github


vcs 中 share 到 git 提交 

远程git会自动创建相应的仓库


---

	…or create a new repository on the command line
	
	echo "# movie" >> README.md
	git init
	git add README.md
	git commit -m "first commit"
	git remote add origin https://github.com/fangmd/movie.git
	git push -u origin master


	…or push an existing repository from the command line
	
	git remote add origin https://github.com/fangmd/movie.git
	git push -u origin master

	…or import code from another repository
	You can initialize this repository with code from a Subversion, Mercurial, or TFS project.