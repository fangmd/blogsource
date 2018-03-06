---
title: Jenkins 使用记录
date: 2017-12-15 12:18:12
tags: [Jenkins]
category: Jenkins

---

环境：macos

# 安装 


## brew 安装(推荐)

```
// 使用brew安装
brew install jenkins
// 启动，直接运行jenkins即可启动服务
jenkins
```

## 下载安装包安装（不推荐）

官方网站 下载后，安装

**注意：**这种方法安装后会有权限问题

```
https://jenkins.io/download/
```

安装完成后会自动打开 `https://localhost:8080` 


# 构建触发器

- 根据提交进行构建（Build when a change is pushed to GitHub）
- 定期进行构建（Build periodically）
- 定期检测代码更新，如有更新则进行构建（Poll SCM）

# 问题1

`/Users/Shared/Jenkins/Home/secrets/initialAdminPassword` 文件打不开

处理方法：[https://stackoverflow.com/questions/37146063/how-do-i-get-initial-admin-password-for-jenkins-on-mac](https://stackoverflow.com/questions/37146063/how-do-i-get-initial-admin-password-for-jenkins-on-mac)

1. finder 打开 `/Users/Shared/Jenkins/Home` 目录，

2. 设置 `secrets` 文件夹的权限，

3. 设置 `initialAdminPassword` 文件的权限

获取密码


# 问题2

卡在创建用户的界面了

处理：不创建用户，在 `save And Finish` 按钮的左边有个 使用 admin 登入的按钮

# 创建用户

`系统管理` -》`管理用户`

# 启动 & 停止

```
设置开机自启动：sudo launchctl load -w /Library/LaunchDaemons/org.jenkins-ci.plist
取消开机自启动：sudo launchctl unload -w /Library/LaunchDaemons/org.jenkins-ci.plist
手动启动：Java -jar jenkins.war
后台启动(默认端口)：nohup java -jar jenkins.war &
后台启动(指定端口)：nohup java -jar jenkins.war -httpPort=88 &
后台启动(HTTPS)：nohup java -jar jenkins.war -httpsPort=88 &

启动 ： sudo launchctl load /Library/LaunchDaemons/org.jenkins-ci.plist

停止： sudo launchctl unload /Library/LaunchDaemons/org.jenkins-ci.plist
```

