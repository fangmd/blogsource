---
title: Git 安装
date: 2016-03-28 08:00:35 
tags: git
categories: program

---



# Git 安装
## 下载 git
## 安装过程
需要注意的：

![](https://xuanwo.org/imgs/opinion/Git-path-setting.png)

>Git的默认设置下，出于安全考虑，只有在Git Bash中才能进行Git的相关操作。按照上图进行的选择，将会使得Git安装程序在系统PATH中加入Git的相关路径，使得你可以在CMD界面下调用Git，不用打开Git Bash了。

<!--more-->

检查git是否安装成功：

	git --version

出现版本号说明安装成功。

## 连接到GitHub
### 设置Git的user name 和 email

	git config --global user.name "yourname"
	git config --global user.email "youremail"

### 生成密钥
	ssh-keygen -t rsa -C "fang@yeah.net"

连续3个回车。如果不需要密码的话。

最后得到了两个文件：id_rsa和id_rsa.pub。

### 添加密钥到ssh-agent
添加生成的 SSH key 到 ssh-agent。

	ssh-add ~/.ssh/id_rsa

### 登陆Github, 添加 ssh 。
>把id_rsa.pub文件里的全部内容复制到里面

### 连接
	ssh -T git@github.com

看到

	The authenticity of host 'github.com (207.97.227.239)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)?

选择`yes`

	  Hi fangmd! You've successfully authenticated, but GitHub does not provide shell access.

连接成功。可以在GitHub上看到ssh是在使用状态。

### git 设置状态确定
可以看到账户信息

	git config --list


# 问题记录

## ssh-add 

```
[root@localhost .ssh]# ssh-add git_is_rsa
Could not open a connection to your authentication agent.


处理：
```
eval "$(ssh-agent)"
```