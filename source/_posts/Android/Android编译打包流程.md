---
title: Android 编译打包流程
date: 2019-01-18 14:18:12
tags: gradle
category: android

---


Android 工程使用 gradle 编译和打包工程。


## Gradle 构建主要流程

Gradle 构建过程主要包含三个阶段：

1. 初始化阶段

读取 `setting.gradle` 中的 include 信息，确定有多少个工程加入到构建。

每个工程中都有对应的 `build.gradle`。

2. 配置阶段

根据每个工程目录下的 `build.gradle`, 配置 gradle 对象，并构建好依赖有向图。

3. 执行阶段

根据配置阶段拿到的配置信息和任务依赖有向图执行对应的task。

## Android 应用源码到生成 Apk 流程

![https://img-blog.csdn.net/20151027093232966](https://img-blog.csdn.net/20151027093232966)


```
java -> dex -> apk
```





参考：[https://cloud.tencent.com/developer/article/1032349](https://cloud.tencent.com/developer/article/1032349)