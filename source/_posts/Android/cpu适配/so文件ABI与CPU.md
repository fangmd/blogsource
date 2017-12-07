---
title: so 文件 和 ABI 和CPU
date: 2017-12-07 08:00:35
tags: NDK
categories: android

---


# CPU 与 ABI

1. 通常 CPU 都会支持多种 ABI
2. 在应用安装的时候，只有该设备 CPU 支持的 .so 文件会被安装

cpu 支持 ABI 表格：

![cpu-abi](https://github.com/fangmd/markdownphoto/raw/master/src/cpu%E9%80%82%E9%85%8D/cpu-abi.png)

>注意：上表格中的空白部分，是我不知道它是否支持，极有可能是不支持

[ABI Management 官方文档](https://developer.android.com/ndk/guides/abis.html)

<!--more-->


- x86 设备选择 ABI 优先级
	- libs/x86目录中如果存在.so文件的话，会被安装
	- 如果不存在，则会选择armeabi-v7a中的.so文件
	- 如果也不存在，则选择armeabi目录中的.so文件

>x86 设备不能保证完全运行 arma类型 so包

3. 64位设备（arm64-v8a, x86_64, mips64）能够运行32位的函数库
4. 会安装优先级较高的ABI目录，则其它优先级较低的ABI目录（包括其它module中的ABI目录），都无法安装。例如：在cpu是ARMv7架构的手机上，如果检测到armeabi-v7a，就会选择安装armeabi-v7a，则armeabi下的文件，都无法安装了。


# 工具

1. 检查应用安装到手机后 so 包安装情况 [Native Libs Monitor](https://play.google.com/store/apps/details?id=com.xh.nativelibsmonitor.app)



# 常见问题

## 第三方库没有完整的 so 包

项目中使用了两个第三方库

A库提供了 armeabi-v7 so 包 和 armeabi so包
B库只提供了 armeabi-v5 so 包

如果在 armv7 架构的手机中运行项目就会报找不到 B库 so 包的错误。

解决方案一：将 B库 中的 armeabi-v5 so 包 放在 armeabi 目录下。将 A库 中的 armeabi so 包 放在 armeabi 目录下






参考

- [http://blog.csdn.net/xx326664162/article/details/51167849](http://blog.csdn.net/xx326664162/article/details/51167849)
- [http://blog.csdn.net/xx326664162/article/details/51163905](http://blog.csdn.net/xx326664162/article/details/51163905)




