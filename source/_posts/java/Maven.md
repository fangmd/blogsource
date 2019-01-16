---
title: Maven
date: 2018-12-21 21:18:12
tags: [java]
category: java基础
---


Maven 作用

1. 依赖管理
2. 项目一键构建 (编译 测试(junit) 运行 打包 部署)


# 常用命令

```
mvn tomcat:run
mvn clean
//编译主目录文件，不包含 test 中的代码，
mvn compile
//编译测试目录下的文件, 并执行测试
mvn test
//打包
mvn package
//项目发布到本地仓库
mvn install
```

maven 三种生命周期:

1. Clean: clean
2. Default: compile, test, package, install, deploy
3. Site: site

# HelloWorld

[https://medium.com/@backslash112/create-maven-project-with-servlet-in-intellij-idea-2018-be0d673bd9af](https://medium.com/@backslash112/create-maven-project-with-servlet-in-intellij-idea-2018-be0d673bd9af)

# 版本冲突处理

1. exclude
2. 版本强制锁定





