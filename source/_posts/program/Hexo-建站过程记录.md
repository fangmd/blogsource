---
title: Hexo 建站过程记录
date: 2016-03-25 22:00:35
tags: hexo
categories: program

---
# Hexo搭建博客
>记录搭建博客的全过程

## 软件准备

- [Node.js](http://nodejs.org/) 环境
- [GitHub桌面版](https://desktop.github.com/)

安装过程全按默认设置安装。

GitHub桌面版安装时会下载文件，可能会很慢。

解决办法：来自：[知乎](https://www.zhihu.com/question/23110947)



1. 下载：[github离线包](http://pan.baidu.com/s/1Hkewm) 
2. 解压文件到本地，打开文件`GitHub.application`安装

<!--more-->

**确认安装完成：**

1. 打开桌面`Git Shell`
2. 输入`node -v` 回车，如果出现版本号表示安装成功
3. 输入`npm` 回车，出现下图，如果没有就需要配置环境变量：`C:\Program Files\nodejs\node_modules\npm
`
![](http://i.imgur.com/t8geOON.png)
## 安装Hexo
>使用`Git shell`安装

	npm install hexo-cli -g

如果下载很慢，很慢可以使用`npm install -g cnpm --registry=https://registry.npm.taobao.org`

	npm install hexo --save

.

	hexo -v
如果出现版本号表示hexo安装成功

## 配置Hexo本地文件夹
>下面的操作使用cmd，使用git shell 应该也可以

cd到一个目录按自己的喜好定，如`C:\Users\DoubleFang\Documents\GitHub\hexo`

	hexo init
完成后执行：生成public静态文件

	hexo g
出现下图说明成功

![](https://xuanwo.org/imgs/opinion/hexo-g.png)
输入下面命令试运行：本地发布预览效果

	hexo s
会提示：

	INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
使用游览器打开`http://localhost:4000/`如果有出现`Hexo`主页说明成功，没有也没关系，直接同步到github上还是能显示的。

## 同步到Github
### 准备github
1. 注册账号(如果没有的话)
2. 在主页右下角创建`New repository`，name必须和用户名一致如fang.github.io
3. 首次创建耐心等待10分钟左右审核，之后即可访问静态主页如http://fang.github.io

### 同步本地内容
1. 打开`C:\Users\DoubleFang\Documents\GitHub\hexo`目录（即hexo本地文件）下使用记事本打开`_config.yml`，找到，更改为：

		deploy:
		type: git
		repo: git@github.com:fang/fang.github.io.git
		branch: master
2. 使用`git shell`同步

		hexo d

**注意：**这里使用`cmd`会报没有权限的错误。

重新打开：`http://fang.github.io/`会发现网页已经改变了。


初步配置到此结束。

## 上传新的文章
### 方法一：
1. 可以将`。md`文件放到`C:\Users\DoubleFang\Documents\GitHub\hexo\source\_posts`下面然后

		hexo g
		hexo d  
需要在文件前面添加一些属性(方法二中自动生成的内容)，同步到github

### 方法二
	hexo new "my new post"
my new post：表示文件名，在`hexo/source/_posts`中打开这个文件：

	---
	title:    
	date: 2016-03-25 21:18:12
	tags: 
	---
title:标题，tags：标签，在里面写文章

	hexo d 
上传文件


## 出现的问题

1. git deploy not found

		npm install hexo-deployer-git --save

参考资料：[https://xuanwo.org/2015/03/26/hexo-intor/](https://xuanwo.org/2015/03/26/hexo-intor/)
