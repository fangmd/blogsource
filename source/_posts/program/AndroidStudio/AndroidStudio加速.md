---
title: android studio 加速
date: 2017-06-25 11:13:12
tags: AndroidStudio
category: program

---

#  使用并行编译

在 `gradle.properties` 中添加下面的代码：

```
org.gradle.parallel=true
```

<!--more-->

# 开启守护进程

在 `gradle.properties` 中添加下面的代码：

```
org.gradle.daemon=true 
```


# 给 Gradle 设置更大的内存

在 `gradle.properties` 中添加下面的代码：

```
org.gradle.jvmargs=-Xmx1536m
```

# 使用Gradle caching

需要 Gradle 版本 >= 3.5

在 `gradle.properties` 中添加下面的代码：

```
# Set this in gradle.properties
org.gradle.caching=true
```