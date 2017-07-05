---
title: Android Studio 内存泄露检测与解决
date: 2016-04-03 13:18:12
tags: [android studio, 内存泄露]
category: android
---

# Android Studio 内存泄露检测与解决

## 什么是内存泄露
应该被GC回收的对象存在无效引用，而没有被回收，导致占用了内存却没有发挥作用；

## 内存泄露的栗子

### 0

![](http://ww1.sinaimg.cn/large/006tNc79jw1f4etz0ls28j30hs09874q)

`mLeak`是存储在静态区的静态变量，而`Leak`是内部类，它持有了外部类`Activity`的引用，这就导致了`Activity`需要被销毁的时候，由于被`mLead`持有，所以不会被
GC回收，造成内存泄露。

### 1

单例对象中传入`Activity`对象。

解决方法：new Singleton(context)改为new Singleton(context.getApplicationContext())即可

## 检测内存泄露 -- Android Monitor
>Memory栏中看到app的运行内存使用情况

四个按钮：

###  enabled
关闭内存检测
### initiate GC
手动触发GC
### Dump java Heap
进入HPROF Viewer界面，查看Java的Heap

Reference Tree代表指向该实例的引用，可以从这里面查看内存泄漏的原因

Shallow Size指的是该对象本身占用内存的大小，Retained Size代表该对象被释放后，垃圾回收器能回收的内存总和。
###  Start Allocation Tracking






参考：
- [https://gold.xitu.io/entry/574d64bca3413100592a433b/view](https://gold.xitu.io/entry/574d64bca3413100592a433b/view)
