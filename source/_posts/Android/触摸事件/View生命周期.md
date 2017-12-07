---
title: [触摸事件] View 生命周期
date: 2017-12-05 09:13:12
tags: 触摸事件
category: Android

---

# 定义

>This class represents the basic building block for user interface components. A View occupies a rectangular area on the screen and is responsible for drawing and event handling. View is the base class for widgets, which are used to create interactive UI components (buttons, text fields, etc.). The ViewGroup subclass is the base class for layouts, which are invisible containers that hold other Views (or other ViewGroups) and define their layout properties.

这个类是用户交互的基础组件。一个 View 是一个矩形区域用于绘制图形和处理触摸事件。View 是所有 控件类 的基类。ViewGroup 子类是所有 布局控件 的基类。


<!--more-->


# 生命周期

几个重要的方法：

1. Creation 创建阶段

	- Constructors	构造方法
	- onFinishInflate()	该方法当View及其子View从XML文件中加载完成后会被调用。

2. Layout 布局

	- onMeasure(int, int)：决定了 View和其子View 的大小
	- onLayout(boolean, int, int, int, int)：当 View 需要为其子View 分配位置的时候调用
	- onSizeChanged(int, int, int, int)：当 子View 大小改变的时候调用

3. Drawing 绘制

	- onDraw(android.graphics.Canvas)：当 View 需要绘制内容的时候调用

4. Event processing 事件处理

	- onKeyDown(int, KeyEvent)：当一个物理按键按下的时候调用
	- onKeyUp(int, KeyEvent)：当一个物理按键抬起的时候调用
	- onTrackballEvent(MotionEvent)：该方法在一个轨迹球运动事件发生时被调用。
	- onTouchEvent(MotionEvent)：当触摸事件发生的时候调用

5. Focus 聚焦

	- onFocusChanged(boolean, int, android.graphics.Rect)：View 获取或者丢失 焦点的时候调用
	- onWindowFocusChanged(boolean)：当 Window 内的 View 获取或者丢失 焦点的时候调用

6. Attaching 附上

	- onAttachedToWindow()：View 加载到 Windows 上的时候调用
	- onDetachedFromWindow：View 离开 Windows 的时候调用
	- onWindowVisibilityChanged(int)： 当 windows 可见性改变的时候调用

# View 的创建流程

## visible 状态

里面的几个方法会重复多次调用，简化版本：

- 构造方法
- onFinishInflate
- onVisibilityChanged
- onAttachedToWindow
- onWindowVisibilityChanged
- onWindowFocusChanged
- onMeasure
- onLayout
- onDraw

## invisible 状态

里面的几个方法会重复多次调用，简化版本：

- 构造方法
- onFinishInflate
- onVisibilityChanged
- onAttachedToWindow
- onWindowVisibilityChanged
- onWindowFocusChanged
- onMeasure
- onLayout

没有 `onDraw` 方法


# View 的销毁流程

```
onWindowFocusChanged
onWindowVisibilityChanged
onDetachedFromWindow
```

# 总

1. View 设置 `android:visibility=gone` 属性的时候，View 的 layout和drawing 相关方法都不会调用，View 的大小为 0
2. View的销毁流程和可见性没有关系


```
[改变可见性] --> 构造View() --> onFinishInflate() --> onAttachedToWindow() --> onMeasure() --> onSizeChanged() --> onLayout() --> onDraw() --> onDetackedFromWindow()
```


![原文](http://www.jianshu.com/p/08e6dab7886e)

