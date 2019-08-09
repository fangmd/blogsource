---
title: gradle编译优化
date: 2018-11-14 21:03:14
tags: [Android优化]
categories: android

---

# gradle 编译报告生成

```
./gradlew assembleDebug --scan
```

## 关键的点

- `Performance`: 查看各个编译环节花费的时间
- `Performance -> Settings and suggestions`: 优化建议

# 优化

## 更新 gradle

## 优化 gralde 配置

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

参考：

- [How to improve the build speed of your Android projects](https://medium.freecodecamp.org/how-to-improve-the-build-speed-of-your-android-projects-bd49029d8602)
