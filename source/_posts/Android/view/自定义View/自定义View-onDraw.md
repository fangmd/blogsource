---
title: 自定义View -- onDraw
date: 2018-01-19 09:23:14
tags: [自定义View]
categories: Android

---

自定义 View 中的绘制方法

<!--more-->

# 触发这个方法的条件

- 自定义 View 内调用 `invalidate();` 方法， 在主线程中使用
- 自定义 View 内调用 `postInvalidate();` 方法, 在子线程中使用

```java
    /**
     * Invalidate the whole view. If the view is visible,
     * {@link #onDraw(android.graphics.Canvas)} will be called at some point in
     * the future.
     * <p>
     * This must be called from a UI thread. To call from a non-UI thread, call
     * {@link #postInvalidate()}.
     */
    public void invalidate() {
        invalidate(true);
    }

    /**
     * <p>Cause an invalidate to happen on a subsequent cycle through the event loop.
     * Use this to invalidate the View from a non-UI thread.</p>
     *
     * <p>This method can be invoked from outside of the UI thread
     * only when this View is attached to a window.</p>
     *
     * @see #invalidate()
     * @see #postInvalidateDelayed(long)
     */
    public void postInvalidate() {
        postInvalidateDelayed(0);
    }
```


例子：

```java
public void setProgress(float progress) {
    this.mProgress = progress;
    invalidate();
}
```




