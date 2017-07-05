---
title: 自定义View-方法流程
date: 2016-12-28 09:23:14
tags: [View, 自定义View]
categories: Android

---


# 自定义View 整体流程

![https://cdn-images-1.medium.com/max/1600/1*abc0UlGj1myFD0eph4pZjQ.png](https://cdn-images-1.medium.com/max/1600/1*abc0UlGj1myFD0eph4pZjQ.png)

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

# 更新视图

- `invalidate()`: 这个触发 `dispatchDraw() -> draw() -> onDraw()`
- `requestLayout()`: 这个方法会触发自定义View重新测量，触发 `mesure()->onMesure()->...`
