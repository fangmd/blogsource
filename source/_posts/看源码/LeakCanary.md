---
title: LeakCanary2.0 原理
date: 2019-10-29 09:38:14
tags: [源码]
categories: android

---


1. LeakCanary使用ObjectWatcher来监控Android的生命周期。当Activity和Fragment被destroy以后，这些引用被传给ObjectWatcher以WeakReference的形式引用着。如果gc完5秒钟以后这些引用还没有被清除掉，那就是内存泄露了。
2. 当被泄露掉的对象达到一个阈值，LeakCanary就会把java的堆栈信息dump到.hprof文件中。
3. LeakCanary用Shark库来解析.hprof文件，找到无法被清理的引用的引用栈，然后再根据对Android系统的知识来判定是哪个实例导致的泄露。
4. 通过泄露信息，LeakCanary会将一条完整的引用链缩减到一个小的引用链，其余的因为这个小的引用链导致的泄露链都会被聚合在一起。



参考:

- [https://juejin.im/post/5db44d456fb9a0204c5c3291](https://juejin.im/post/5db44d456fb9a0204c5c3291)