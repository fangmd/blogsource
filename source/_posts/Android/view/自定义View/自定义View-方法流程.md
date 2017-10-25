---
title: 自定义View-方法流程
date: 2017-10-25 09:23:14
tags: [View, 自定义View]
categories: Android

---

```
2017/10/15 添加 [http://hencoder.com/ui-1-5/](http://hencoder.com/ui-1-5/) 笔记

```

# 自定义View 整体流程

![https://cdn-images-1.medium.com/max/1600/1*abc0UlGj1myFD0eph4pZjQ.png](https://cdn-images-1.medium.com/max/1600/1*abc0UlGj1myFD0eph4pZjQ.png)

这个图中有错误：dispatchDraw() 应该在 onDraw() 之后执行

## Constructor

构造方法：在这里做 view 的各种初始化操作：

1. 初始化一些值，从 style 中取值
2. 初始化画笔


## onAttachedToWindow

控件显示在界面上的使用调用这个方法。

<!--more-->
## onMeasure

测量方法

通常在这里可以做两种操作：

1. 获取控件的宽高
2. 设置控件的宽高

自定义测量方法，结尾要`setMeasuredDimension(widthSize,heightSize);`

    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)

两个参数封装了宽高的模式和宽高的值：
```java
    int widthMode = MeasureSpec.getMode(widthMeasureSpec);
    int widthSize = MeasureSpec.getSize(widthMeasureSpec);//在不动的模式下Size有不同的意义
```

三种模式：

1. `MeasureSpec.UNSPECIFIED`：表示默认值，父控件没有给子view任何限制。

        // size 没有意义

2. `MeasureSpec.AT_MOST`：表示父控件给子view一个最大的特定值，而子view不能超过这个值的大小；子控件设置成`wrap_content`是这个模式；即specSize是view最大的尺寸。

        // size表示不能超过的值，这里的200表示父控件能提供的最大宽度值
        widthSize = Math.min(200, widthSize);

3. `MeasureSpec.EXACTLY`：表示父控件给子view一个具体的值，子view要设置成这些值的大小；子控件宽高设置为`match_parent`或者`具体的值`时候是这个模式;即子View的大小由specSize决定。

    // size 表示宽度或者高度具体值

参考：[http://blog.csdn.net/cyp331203/article/details/45027641](http://blog.csdn.net/cyp331203/article/details/45027641)

#### MeasureSpe
>描述了父View对子View大小的期望.里面包含了测量模式和大小

通过一个静态方法将大小和模式合成：方法内部知识简单的相加

```java
MeasureSpec.makeMeasureSpec(specSize,specMode);
```

## onLayout
>决定子控件在布局中如何放置

控件中如果有子控件，通过这个方法确定自控件的位置。

onLayout方法:在measure过程结束后，视图大小确定后，接下来就是layout的过程。

```java
protected void onLayout(boolean changed, int l, int t, int r, int b)
```

### 流式布局
一排放满，放下一排

```java
    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        int groupWidth = getMeasuredWidth();
        int groupHeight = getMeasuredHeight();
        int posLeft = l;
        int posTop = t;
        int rowHeight = 0; // 获取单前行中最高的控件的高度
        for (int i = 0; i < getChildCount(); i++) {
            View child = getChildAt(i);
            int childWidth = child.getMeasuredWidth();
            int childHeight = child.getMeasuredHeight();
            rowHeight = Math.max(rowHeight, childHeight);
            if (posLeft + childWidth > groupWidth) {
                posLeft = l;
                posTop += rowHeight;
                rowHeight = 0;
            }
            child.layout(posLeft, posTop, posLeft + childWidth, posTop + childHeight); // 放置子控件
            posLeft += child.getMeasuredWidth();

        }
    }
```

还需要重写测量方法onMeasure，可以让布局文件继承`FrameLayout`来使用它的测量方法

# onDraw

在view的`onLayout()`方法后执行

```java
protected void onDraw(Canvas canvas)
```

绘制条件：画笔-- paint，画布 canvas

由两种情况不需要画笔：drawARGB ， drawColor


## 绘制顺序

参考：[http://hencoder.com/ui-1-5/](http://hencoder.com/ui-1-5/)

### 对于非 ViewGroup 类型的 View，比如 TextView，ImageView

```java
public class AppImageView extends ImageView {  
    ...

    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        if (DEBUG) {            
            myDraw(canvas);
        }
    }
}
```

上面代码中 myDraw 方法如果在 `super.onDraw` 之前执行就会被 ImageView 设置的图片覆盖。

### 对于 ViewGroup 类型的 View，比如 LinearLayout

在 `super.onDraw` 后面写的 `myDraw` 绘制的内容会被 子View 覆盖

```
onDraw() 绘制主体

dispatchDraw() 绘制 子View
```


如果要让绘制的内容不被 子View 覆盖, 就需要让 `myDraw` 方法在  `super.dispatchDraw()`  之后执行：

```java
public class SpottedLinearLayout extends LinearLayout {  
    ...

    // 把 onDraw() 换成了 dispatchDraw()
    protected void dispatchDraw(Canvas canvas) {
       super.dispatchDraw(canvas);

       ... // 绘制斑点
    }
}
```

## 自定义 View 绘制过程简述

1. 背景
2. 主体（onDraw()）
3. 子 View（dispatchDraw()）
4. 滑动边缘渐变和滑动条
5. 前景

View.java 中的 draw 方法：

```java
    public void draw(Canvas canvas) {
        final int privateFlags = mPrivateFlags;
        final boolean dirtyOpaque = (privateFlags & PFLAG_DIRTY_MASK) == PFLAG_DIRTY_OPAQUE &&
                (mAttachInfo == null || !mAttachInfo.mIgnoreDirtyState);
        mPrivateFlags = (privateFlags & ~PFLAG_DIRTY_MASK) | PFLAG_DRAWN;

        /*
         * Draw traversal performs several drawing steps which must be executed
         * in the appropriate order:
         *
         *      1. Draw the background
         *      2. If necessary, save the canvas' layers to prepare for fading
         *      3. Draw view's content
         *      4. Draw children
         *      5. If necessary, draw the fading edges and restore layers
         *      6. Draw decorations (scrollbars for instance)
         */

        // Step 1, draw the background, if needed
        int saveCount;

        if (!dirtyOpaque) {
            drawBackground(canvas);
        }

        // skip step 2 & 5 if possible (common case)
        final int viewFlags = mViewFlags;
        boolean horizontalEdges = (viewFlags & FADING_EDGE_HORIZONTAL) != 0;
        boolean verticalEdges = (viewFlags & FADING_EDGE_VERTICAL) != 0;
        if (!verticalEdges && !horizontalEdges) {
            // Step 3, draw the content
            if (!dirtyOpaque) onDraw(canvas);

            // Step 4, draw the children
            dispatchDraw(canvas);

            // Overlay is part of the content and draws beneath Foreground
            if (mOverlay != null && !mOverlay.isEmpty()) {
                mOverlay.getOverlayView().dispatchDraw(canvas);
            }

            // Step 6, draw decorations (foreground, scrollbars)
            onDrawForeground(canvas);

            // we're done...
            return;
        }
```

简化版本：

```java
public void draw(Canvas canvas) {  
    ...

    drawBackground(Canvas); // 绘制背景（不能重写）
    onDraw(Canvas); // 绘制主体
    dispatchDraw(Canvas); // 绘制子 View
    onDrawForeground(Canvas); // 绘制滑动相关和前景

    ...
}
```

![https://ws3.sinaimg.cn/large/006tKfTcly1fii5jk7l19j30q70e0di5.jpg](https://ws3.sinaimg.cn/large/006tKfTcly1fii5jk7l19j30q70e0di5.jpg)

### 注意

1. 出于效率的考虑，ViewGroup 默认会绕过 draw() 方法，换而直接执行 dispatchDraw()，以此来简化绘制流程。所以如果你自定义了某个 ViewGroup 的子类（比如 LinearLayout）并且需要在它的除  dispatchDraw() 以外的任何一个绘制方法内绘制内容，你可能会需要调用  View.setWillNotDraw(false) 这行代码来切换到完整的绘制流程（是「可能」而不是「必须」的原因是，有些 ViewGroup 是已经调用过 setWillNotDraw(false) 了的，例如 ScrollView）。
2. 





# 更新视图

- `invalidate()`: 这个触发 `dispatchDraw() -> draw() -> onDraw()`
- `requestLayout()`: 这个方法会触发自定义View重新测量，触发 `mesure()->onMesure()->...`
