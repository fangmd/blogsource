---
title: 数据存储 - SharedPreferences
date: 2016-11-03 13:18:12
tags: [数据存储, SharedPreferences]
category: android

---

# 作用
>使用键值对的形式存储数据

一般用于存储一些app有关的设置，属于轻量级的数据存储。

# 基本使用

## 获取SharedPreferences 对象
1. 方法一：Context类中`getSharedPreferences()`方法

	参数1：文件名称
	参数2：操作模式： MODE_PRIVATE 和 MODE_MULTI_PROCESS

2. 方法二：Activity类中`getPreferences()`的方法

	和Context类的方法相似，不过只需要一个参数：操作模式；文件名是这个活动的类名；
3. PreferenceManager类中的getDefaultSharedPreferences()方法

	静态方法，参数：Context；使用当前应用程序包名作为前缀来命名文件；

<!--more-->

## 存储数据
1. 获取SharedPreferences.Editor对象

		SharedPreferences.Editor edit = sp.edit();
2. 存入数据

		edit.put.....
3. `edit.commit()`提交数据

## 获取数据

	sp.get.....;//参数1：键，参数2：默认值

# SharedPreferences 创建有两种方式

## getPreferences()

属于 Activity 级别，每个 Activity 会有自己的 SharedPreferences 文件

## getSharedPreferences()

属于 Application 级别，项目中的各个位置都能使用。

# SharedPreferences 保存数据的两种方式

## apply

Api 9 后提供的方法，调用这种方式存储数据时，数据会先保存到内存中，让后异步存储到 SharedPreferences 文件中，不会阻塞主线程。

**缺点：**如果数据可能会马上用到不建议这种存储方式。

## commit

API 1

在调用线程中执行存储，会阻塞线程知道数据保存结束。

# SharedPreferences 可以存储的数据类型

基本数据类型：boolean, float, int, long, strings

API3.0 后可以存储 Set<String>
