---
title: NDK 学习记录
date: 2016-06-12 13:18:12
tags: [ndk, 官方文档]
category: android

---

# ndk-build

[http://blog.csdn.net/shulianghan/article/details/18964835](http://blog.csdn.net/shulianghan/article/details/18964835)



# 新方法 CMake

[http://wl9739.github.io/2016/09/21/%E5%9C%A8-Android-Studio-2-2-%E4%B8%AD%E6%84%89%E5%BF%AB%E5%9C%B0%E4%BD%BF%E7%94%A8-C-C-md/](http://wl9739.github.io/2016/09/21/%E5%9C%A8-Android-Studio-2-2-%E4%B8%AD%E6%84%89%E5%BF%AB%E5%9C%B0%E4%BD%BF%E7%94%A8-C-C-md/)

# NDK 官方文档 学习

## 环境配置
1. 下载
2. 解压
3. 配置环境变量

## 概述

### Introduction
可能使用到ndk的人：

1. 需要开发多平台app的人
2. 提供 libraries 给他人使用的人
3. 游戏开发等需要代码运行速度的人

### How it Works

#### Main components
需要了解下面的组件

1. ndk-build：脚本文件，在NDK开发的时候会执行
	- 自动识别开发系统和应用的工程文件
	- 生成二进制文件
	- 复制二进制文件到应用工程目录中
	
2. java：android构建的时候生成`.dex`文件，运行在java虚拟机中，java语言使用下面的语句调用`native`代码

		public native int add(int x,int y);

3. Native shared libraries:NDK会把native code 构建成`.so`文件
4. Native static libraries:构建静态库
5. Java Native Interface(JNI):java 和 C++ 交互
6. Application Binary Interface(ABI):决定了代码如何和设备交互，不同的设备需要不同的ABI文件来支持（俗称的CUP适配）
7. Manifest:如果要写一个没有java 组件的app，需要声明`NativeActivity`类

如果要使用`ndk-build`脚本和调试脚本`ndk-gdb`需要下面两项：

1. `Android.mk`:需要创建`Android.mk`配置文件在`jni`文件夹下，`ndk-build`脚本会寻找这个文件，它定义了一些配置信息(defines the module and its name, the source files to be compiled, build flags and libraries to link.)
2. `Application.mk`:这个文件列举和描述了app使用的模块，包括：
	- ABIs 
	- Toolchains
	- Standard libraries
	
#### Flow 使用流程
1. 设计app，确定哪里需要使用native代码
2. 创建app工程
3. 如果作纯 native代码的app，需要在`AndroidManifest.xml`中配置
4. 创建`Android.mk`文件(describing the native library, including name, flags, linked libraries, and source files to be compiled in the "JNI" directory.)
5. 可选，创建`Application.mk`
....

### Native Activities and Applications
>暂时跳过

## Android.mk

### Overview
`Android.mk`保存在工程目录下的`jin/`文件夹下

### Basics

`Android.mk`文件开始句子：

	LOCAL_PATH := $(call my-dir)

这个句子表明了开发的文件位置，宏功能`my-dir`由构建系统提供，返回当前的文件目录

下个句子：`CLEAR_VARS`:值由构建系统提供

	include $(CLEAR_VARS)
这个变量指出GNU清理大多数的`LOCAL_XXX`变量，比如：`LOCAL_MODULE`，`LOCAL_SRC_FILES`，`LOCAL_STATIC_LIBRARIES`，但是不会清理`LOCAL_PATH`

next: `LOCAL_MODULE`:保存了模块的名称，每个模块都要使用语句

	LOCAL_MODULE := hello-jni

注意：模块名必须是唯一的而且不带空格，构建系统在构建模块的时候会自动添加文件名的前缀和后缀比如：`libhello-jni.so`,如果模块名中和前缀后缀有重合的地方，就不会添加前缀或者后缀了。

next：

	LOCAL_SRC_FILES := hello.jni.c

包含了c或者c++文件名

last：

	include $(BUILD_SHARED_LIBRARY)

## Application.mk


## ndk-build



