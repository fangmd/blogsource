---
title: Android内存优化 -- 内存泄露的原因
date: 2016-05-14 21:03:14
tags: [Android优化,内存泄露]
categories: android

---

# Android内存优化 -- 内存泄露的原因

- 注册没取消造成内存泄露，如：广播
- 静态变量持有Activity的引用
- 单例模式持有Activity的引用
- 查询数据库后没有关闭游标cursor
- 构造Adapter时，没有使用 convertView 重用
- Bitmap对象不在使用时调用recycle()释放内存
- 对象被生命周期长的对象引用，如activity被静态集合引用导致activity不能释放
- 使用Handler造成的内存泄露