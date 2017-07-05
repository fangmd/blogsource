---
title: Intent传值和Intent+Bundle传值
date: 2016-06-03 13:18:12
tags: [Intent, 传值, Bundle]
category: android

---

# Intent传值和Intent+Bundle传值

## 前言
在Android中Intent本身就能传值，但是为什么又要加入Bundle传值？

## 使用Intent + Bundle 的好处
Bundle实现了对传递数据的封装。

### 0 
当A-->B-->C界面跳转的时候：

如果中间传递的数据都一样，那么Bundle对象就可以多次使用，而不用在Intent中反复的获取和放入数据；

使传递过程中的代码更简洁；

## 1
当A-->B; A-->C界面跳转的时候：

如果两种跳转要传递的数据相同或者部分相同，就可以使用同一个Bundle对象，而不用多次写Intent的放入数据方法；



参考：
- [http://blog.csdn.net/garretly/article/details/6207950](http://blog.csdn.net/garretly/article/details/6207950)
