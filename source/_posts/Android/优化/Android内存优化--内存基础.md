---
title: Android内存优化 -- 内存基础
date: 2016-05-14 21:03:14
tags: [Android优化,内存基础]
categories: android

---

# Android内存优化 -- 内存基础

### 物理内存与进程内存

物理内存即移动设备上的RAM，当启动一个Android程序时，会启动一个Dalvik VM进程，系统会给它分配固定的内存空间（16M,32M不定），这块内存空间会映射到RAM上某个区域。然后这个Android程序就会运行在这块空间上。Java里会将这块空间分成Stack栈内存和Heap堆内存。stack里存放对象的引用，heap里存放实际对象数据。

在程序运行中会创建对象，如果未合理管理内存，比如不及时回收无效空间就会造成内存泄露，严重的话可能导致使用内存超过系统分配内存，即内存溢出OOM，导致程序卡顿甚至直接退出。

<!--more-->

### 内存泄露（Memory Leak）

Java内存泄漏指的是进程中某些对象（垃圾对象）已经没有使用价值了，但是它们却可以直接或间接地引用到gc roots导致无法被GC回收。Dalvik VM具备的GC机制（垃圾回收机制）会在内存占用过多时自动回收，严重时会造成内存溢出OOM。

### 内存溢出OOM
当应用程序申请的java heap空间超过Dalvik VM HeapGrowthLimit时，溢出。

注意：OOM并不代表内存不足，只要申请的heap超过Dalvik VM HeapGrowthLimit时，即使内存充足也会溢出。效果是能让较多进程常驻内存。

### 如果RAM不足时系统会做什么？

Android的Memory Killer会杀死优先级较低的进程，让高优先级进程获取更多内存。

### Android系统默认内存回收机制

进程优先级：Foreground进程、Visible进程、Service进程、Background进程、Empty进程;

如果用户按Home键返回桌面，那么该app成为Background进程；如果按Back返回，则成为Empty进程
ActivityManagerService直接管理所有进程的内存资源分配。所有进程要申请或释放内存都需要通过ActivityManagerService对象。

垃圾回收不定期执行。当内存不够时就会遍历heap空间，把垃圾对象删除。
堆内存越大，则GC的时间更长