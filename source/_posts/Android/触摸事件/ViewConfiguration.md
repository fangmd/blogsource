---
title: [触摸事件] ViewConfiguration 类
date: 2017-12-05 09:13:12
tags: 触摸事件
category: Android

---


# 是什么

>Contains methods to standard constants used in the UI for timeouts, sizes, and distances.

这个类中包含一系列的关于视图的静态常量。

# 有哪些常量

>有很多，下面列举几个可能用到的：

滚动条的消失动画时间：（自己制作类似动画的时候可以参考使用这个值）

```java
private static final int SCROLL_BAR_FADE_DURATION = 250;
```

用户滚动的时候，滚动条出现动画的延迟时间（自己制作类似动画的时候可以参考使用这个值）

```java
private static final int SCROLL_BAR_DEFAULT_DELAY = 300;
```

短按和长按的区别时间差：

```java
private static final int DEFAULT_LONG_PRESS_TIMEOUT = 500;
```


处理触摸事件的时候，用户手指滑动大于这个值才被视为有效触摸：

```java
private static final int TOUCH_SLOP = 8;
```


# 使用

```java
private int mTouchSlop = ViewConfiguration.get(getContext()).getScaledTouchSlop();


// 例子：滑动大于 mTouchSlop 才开始处理触摸事件

...
case MotionEvent.ACTION_MOVE:
	if (Math.abs(dDownX) > mTouchSlop || Math.abs(dDownY) > mTouchSlop) {

	}
...
```




