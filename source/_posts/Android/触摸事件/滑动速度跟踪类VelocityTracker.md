---
title: [触摸处理] 滑动速度跟踪类 VelocityTracker
date: 2017-12-05 21:38:14
tags: 触摸处理
categories: android

---

滑动速度跟踪类 VelocityTracker

# 使用流程

通过静态方法 `obtain` 获取速度检测对象（可以在 MotionEvent.ACTION_DOWN 中执行）

```java
tracker = VelocityTracker.obtain();
```

开始检测滑动：(可以在 MotionEvent.ACTION_MOVE 中执行)

```java
tracker.addMovement(event);
```

开始计算, 获取本次触摸的速度（在 MotionEvent.ACTION_UP 中执行）:

```java
tracker.computeCurrentVelocity(int units)
tracker.getXVelocity(int id)
tracker.getYVelocity(int id)
```