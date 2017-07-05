---
title: Android内存优化 -- 布局优化
date: 2016-05-14 21:03:14
tags: [Android内存优化,布局优化]
categories: android

---

# Android内存优化 -- 布局优化

## 布局的层级不能太深
使用Hierarchy Viewer查看UI布局层级

## include 标签把可复用的布局抽取出来

## merge标签
可以优化UI结构，删除多余的层级

## ViewStub
一个隐藏的不占用内存空间的视图对象，可以在运行时延迟加载布局资源文件。

