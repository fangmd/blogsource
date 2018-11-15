---
title: gradle编译优化
date: 2018-11-14 21:03:14
tags: [Android优化]
categories: android

---



gradld 配置:

```
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8

# 开启并行编译
org.gradle.parallel=true
# 开启孵化模式
org.gradle.configureondemand=true

# 开启守护进程
org.gradle.daemon=true

# 开启缓存
android.enableBuildCache=true

```

不检查png的合法性:

```
android {
    aaptOptions {
        cruncherEnabled false
    }
}
```
