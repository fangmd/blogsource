---
title: Android Studio 调试技巧 
date: 2016-04-03 13:18:12
tags: [android studio,调试]
category: android

---

# Android Studio 调试技巧

## debug调试面板

![android studio 调试面板](http://upload-images.jianshu.io/upload_images/142377-0d297eaa70d7e7db?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分为6个功能区：

1. 调试功能区
2. 断点管理功能区
3. 求值表达式
4. 线程帧栈区
5. 对象变量区
6. 变量观察区

### 1 调试功能区

- Step over：单步跳入，执行该操作将导致程序向下执行一行。如果下一行是自定义的方法，则进入该方法内部继续执行，需要注意如果下一行是类库中的方法，则不会方法内部。
- step into：如果下一行是一个方法，则不管该方法是我们自行定义还是类库提供的，都能跳入到方法内部继续执行
- force step into
- step out
- drop frame：停止当前方法的执行，返回到当前方法被调用处，并且所有上下文变量的值也恢复到那个时候


### 2 断点管理区
- resume program:从当前断点移动到下一个断点处，两个断点之间的代码自动被执行
- paush program
- stop app:停止调试
- view Breakpoints:管理所有的断点


### 3 求值表达式

### 4 线程帧栈区

### 5 对象变量区
- 修改变量值

![](http://upload-images.jianshu.io/upload_images/142377-bed2e43c1e8e7bb6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6 变量观察区
添加要观察的变量：

![](http://upload-images.jianshu.io/upload_images/142377-55857cbdbab4a6fd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 线程信息
![](http://upload-images.jianshu.io/upload_images/142377-3243f0036dabba1a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 断点
分为五类

### 0 条件断点
在特定条件发生的断点

使用方法：在断点`红点`上点击右键，在`Condition`项中填入条件，比如:

	s.equals("2q")


### 1 日志断点
该类型的断点不会使程序停下来，而是在输出我们要它输出的日志信息，然后继续执行。

和log差不多

使用方法：同样在断点处右键单击，在弹出的对话框中取消选中Suspend。在弹出的控制面板中，选中Log evaluated expression，然后再填写想要输出的日志信息，如下：

![http://upload-images.jianshu.io/upload_images/142377-d2d5d9a18976ffc8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240](http://upload-images.jianshu.io/upload_images/142377-d2d5d9a18976ffc8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 2 异常断点
一旦发生异常（可以指定某类异常），则会立刻定位到异常抛出的地方。

在上线之前，进行异常断点调试非常有利于减少正式环境中发生crash的几率。

使用方法：在Run菜单项中，选择View Breakpoints，在弹出的面板中添加java exception breakpoints（打钩）


### 3 方法断点
在方法声明上打断点

### 4 属性断点

当我们某个字段值被修改的时候，程序暂停在修改处。通常在调试多线程时尤为可用，能帮我们及时的定位并发错误的问题。

使用方法：在成员变量所在的位置打断点

## 调试的两种方式

![http://upload-images.jianshu.io/upload_images/142377-41dc709f2d22354f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240](http://upload-images.jianshu.io/upload_images/142377-41dc709f2d22354f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Debug

以调试模式安装运行，断点可以在运行之前设置，也可在运行后设置，是多数人最常用的调式方式

### Attach process
和Debug方式相比，能够将调试器attach到任何正在运行的进程。比如，我们可以通过attach process到想要调试的进程。然后，在需要的地方设置相关断点即可。





参考：
- [http://www.jianshu.com/p/011eb88f4e0d](http://www.jianshu.com/p/011eb88f4e0d)
