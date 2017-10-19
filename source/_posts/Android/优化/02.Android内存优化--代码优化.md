---
title: Android内存优化 -- 代码优化
date: 2016-05-14 21:03:14
tags: [Android内存优化,代码优化]
categories: android

---

# Android内存优化 -- 代码优化

## AndroidLint分析代码
推荐使用findBugs 插件

## 尽量不使用枚举及IOC框架，反射

## 常量加 static

## 较少不必要的对象，成员变量

## 尽量使用线程池

尽量使用Android中实现好的线程类，比如：Timer

## 适当使用软引用和弱引用

## 尽量使用静态内部类，避免潜在的内存泄露

## 图片缓存，采用内存缓存LRUCache和硬盘缓存DiskLRUCache

<!--more-->