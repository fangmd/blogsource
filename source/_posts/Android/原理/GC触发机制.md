---
title: Android Dalvik 虚拟机 GC 触发机制
date: 2019-01-18 12:18:12
tags: JVM
category: android

---


## 使用 adb 查看设备分配的内存信息

下面数据来自：Mi8

```
> adb shell getprop dalvik.vm.heapgrowthlimit
无

> adb shell getprop dalvik.vm.heapmaxfree
8m
堆最大空闲值

> adb shell getprop dalvik.vm.heapminfree
512k
堆最小空闲值，GC 后

> adb shell getprop dalvik.vm.heapsize
512m
最大可以使用的内存

> adb shell getprop dalvik.vm.heapstartsize
8m
Java 堆的启始大小

> adb shell getprop dalvik.vm.heaptargetutilization
0.75
堆目标利用率
```

LiveSize: 表示存活对象占用内存大小
堆的实际大小 = LiveSize/利用率
堆的实际大小 > LiveSize + MinFree
堆的实际大小 < LiveSize + MaxFree

>堆的实际大小不符合上面要求就会进行调整(GC+调整内存分配)











参考：[https://juejin.im/post/59eaf830f265da431d3bd27b](https://juejin.im/post/59eaf830f265da431d3bd27b)