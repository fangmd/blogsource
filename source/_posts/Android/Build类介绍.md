---
title: Build 类
date: 2016-03-21 21:38:14
tags: Build类
categories: android

---

# Build 类
>Information about the current build, extracted from system properties.

保存了当前软件的编译信息，和系统的属性信息。

### 两个内部类

## Build.VERSION

保存了各个版本的值`String`形式

- `BASE_OS`
- `CODENAME`
- `INCREMENTAL`
- `PREVIEW_SDK_INT`
- `RELEASE`
- `SDK_INT`
- `SECURITY_PATCH`

## Build.VERSION_CODES
列举了所有安卓版本对应的`Int`值


## 作用
判断当前运行app的手机的版本的高低：

	Build.VERSION.SDK_INT > Build.VERSION_CODES.JELLY_BEAN