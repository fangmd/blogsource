---
title: Android内存优化 -- 内存泄露的原因
date: 2016-05-14 21:03:14
tags: [Android优化,内存泄露]
categories: android

---

- 注册没取消造成内存泄露，如：广播
- 静态变量持有Activity的引用
- 把控件对象赋值给 static 引用
- 单例模式持有Activity的引用
- 查询数据库后没有关闭游标cursor
- 构造 Adapter 时，没有使用 convertView 重用
- Bitmap 对象不在使用时调用 recycle() 释放内存
- 对象被生命周期长的对象引用，如 activity 被静态集合引用导致 activity 不能释放
- 使用 Handler 造成的内存泄露


# 如何避免内存泄漏

1. 在需要使用 Context 的时候，能使用 Application 的 Context 就使用 Application 的 Context
2. 尽量不要使用非静态内部类。在内部类中引用 Activity 或 View 会造成内存泄漏。处理方法是使用 static 内部类，并 WeakReference 来引用 Activity 或 View。