---
title: Gradle依赖查看
date: 2017-09-28 13:18:12
tags: [Gradle]
category: Android

---


# 方法一 推荐

输出依赖到文件

```
./gradlew -q app:dependencies > log
```


# 方法二

通过引入第三方插件来实现

- 首先在应用app的下的build.gradle 文件上加上apply plugin: 'project-report'
- 然后在项目的根目录下执行gradle命令./gradlew htmlDependencyReport 之后会在Build目录下面生成report文件夹，里面生成的有html，里面会有compile的标签，打开即可看到相关的依赖包情况。


此命令有助于查看依赖的版本，因为有的用的低版本，有的高版本，以及查看重复依赖的情况等等，里面还有其他很多的信息可以参考。




参考：

1. [http://www.wxtlife.com/2017/04/16/check-gradle-dependency/](http://www.wxtlife.com/2017/04/16/check-gradle-dependency/)