---
title: Android Architecture Components 关键点
date: 2018-08-20 12:18:12
tags: [Android 架构]
category: android

---


# ViewModel

用于管理数据,它持有 LiveData. 处理数据持久化,存取等具体逻辑, 相当于 MVP 中的 Presenter.

## LiveData 实现数据驱动型编程

自需要对 View 注册 LiveData.

LiveData 数据变更后，会自动更新 View.

## ViewModel 具有数据共享的作用

同一个 Activity/Fragment 对应同一个 ViewModel

ViewModel 的存在是依赖 Activity 或者 Fragment的，不管你在什么地方获取ViewModel ，只要你用的是相同的Activity 或者 Fragment，那么获取到的ViewModel将是同一个 (前提是key值是一样的)，所以ViewModel 也具有数据共享的作用！








参考：

- [https://www.jianshu.com/p/e8955f525f4c](https://www.jianshu.com/p/e8955f525f4c)