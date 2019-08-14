---
title: Mac 下载和编译Android源码
date: 2017-02-26 09:38:14
tags: [Android Source Code]
categories: Android

---


# 基本环境搭建

## Creating a case-sensitive disk image 创建一个大小写敏感的磁盘镜像

如果不这样做，后续会报错：
```
build/core/main.mk:95: ************************************************************
build/core/main.mk:96: You are building on a case-insensitive filesystem.
build/core/main.mk:97: Please move your source tree to a case-sensitive filesystem.
build/core/main.mk:98: ************************************************************
build/core/main.mk:99: *** Case-insensitive filesystems not supported.
make: *** [out/build-aosp_arm64.ninja] Error 1
```

### 创建分区方法
```
sudo hdiutil create -type SPARSE -fs 'Case-sensitive Journaled HFS+' -size 80g ~/android.dmg
```

上面定义了 dmg 的最大容量为 80g，这里的大小并非 dmg 的实际大小，dmg 的实际大小由内部的文件大小决定

通过下面的代码可以重新设置 dmg 的大小：
```
sudo hdiutil resize -size    <new-size-you-want>g  ~/android.dmg.sparseimage
```

### 加载分区:
```
sudo hdiutil attach ~/android.dmg.sparseimage -mountpoint /Volumes/android
```

### 设置文件描述符限制
Mac 系统下默认只能同时打开 1024 个文件，而在进行Android源码编译时有可能会超出这个限制，因此需要解除这个限制。在~/.bash_profile中添加以下内容：
```
ulimit -S -n 1024
```



[http://www.jianshu.com/p/504b53fa6592](http://www.jianshu.com/p/504b53fa6592)
[官方文档](http://source.android.com/source/initializing.html#setting-up-a-mac-os-x-build-environment)

