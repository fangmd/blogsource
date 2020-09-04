---
title: Jenkins Flutter 实现自动化打包
date: 2020-07-07 12:18:12
tags: [Jenkins]
category: Jenkins

---

准备一台 mac

# 1. 安装 jenkins

[https://www.jenkins.io/download/lts/macos/](https://www.jenkins.io/download/lts/macos/)

```
// 使用brew安装
brew install jenkins-lts

// 命令后后台启动
brew services start jenkins-lts

// 重启 Jenkins
Restart the Jenkins service: 
brew services restart jenkins-lts

// 升级 Jenkins
Update the Jenkins version: 
brew upgrade jenkins-lts

// 停止 jenkins
brew services stop jenkins
```


打开：`http://localhost:8080/`

登入后保存密码:

```
http://localhost:8080/
```

## 其他

1. 初始化密码存储位置: `/Users/double/.jenkins/secrets/initialAdminPassword`


# 2. 安装需要的插件

1. Keychains and Provisioning Profiles Management（iOS证书管理）
2. Xcode integration（iOS专用）
3. Gradle plugin（Android专用）



keychains 和 provision 在系统中的路径。

```
keychain: /Users/zcating/Library/Keychains
provision: /Users/zcating/Library/MobileDevice/Provisioning Profiles
```




