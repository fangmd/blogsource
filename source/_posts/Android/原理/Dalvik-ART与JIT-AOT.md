---
title: Dalvik，ART与JIT，AOT
date: 2019-01-18 12:18:12
tags: JVM
category: android

---


Android java 虚拟机有两种：Dalvik, ART(Android RunTime)

编译策略：JIT, AOT

## 虚拟机

将 Dex 翻译成机器码交给系统调用。

Dalvik: 支持 JIT 编译模式

ART: 支持 JIT, AOT 编译模式

### Dalvik

与 JVM 区别

1. Dalvik 基于寄存器(编译的时候话费时间更短)，JVM 基于栈
2. Dalvik 字节码中，局部变量赋给65536个可用的寄存器中的任何一个，Dalvik 指令直接操作这些寄存器，而不是访问堆栈中的元素
3. Dalvik 有自己的字节码，不使用 java 字节码


### GC 机制

- 如何判断是否可以回收

引用计数法


ART GC 机制： (Android8.0) 开始使用 并发复制GC

#### 并发复制GC
简单说：就是通过分配2个space，用来将已被标记的对象全部拷贝到另外一个space，这个space就作为下一次进行内存分配的空间，然后循环往复，两个space不断交换。

过程：

1. 暂停阶段
2. 复制阶段
3. 回收阶段




## JIT

Just in time compiler. 即时编译技术

JIT 在 Android2.2 到 Android4.4 版本（7.0版本也有，后文会叙述），JIT的目的是为了提高Android的运行效率。

JIT 做法/优点：App 运行的时候遇到新的类，就会对这个类进行即时编译成机器码，下次执行的时候就不需要编译可以直接执行。（只是减少重复编译的过程）

缺点：

1. 每次启动都需要重新编译(没有缓存)
2. 耗电量大

## AOT              

Ahead of Time, 提前编译，

1. 属于静态编译，在 apk 安装的时候就会将 dex 编译成 ELE 文件，在以后程序运行的时候就不需要编译了。
2. 对 GC 过程也做了改进

缺点：

1. 安装时间长：安装的时候需要编译
2. 安z装后文件占用空间大

## JIT + AOT

Android 7.0上，JIT 编译器被再次使用，采用AOT/JIT 混合编译的策略，特点是：

1. 应用在安装的时候dex不会再被编译
2. App运行时,dex文件先通过解析器被直接执行，热点函数会被识别并被JIT编译后存储在 jit code cache 中并生成profile文件以记录热点函数的信息。
3. 手机进入 IDLE（空闲） 或者 Charging（充电） 状态的时候，系统会扫描 App 目录下的 profile 文件并执行 AOT 过程进行编译。







参考：

- [https://zhuanlan.zhihu.com/p/53723652](https://zhuanlan.zhihu.com/p/53723652)
- [https://mp.weixin.qq.com/s/NiLnZ_oeTNVCaaYiymtSUg](https://mp.weixin.qq.com/s/NiLnZ_oeTNVCaaYiymtSUg)