---
title: 自定义View -- 绘制文字
date: 2016-03-30 09:23:14
tags: [View, 自定义View, 文字]
categories: Android

---

# 绘制文字

## 方法一 常用

Canvas 调用下面的方法

```java
public void drawText(@NonNull String text, float x, float y, @NonNull Paint paint)
```

- 参数1: 需要绘制的文本
- 参数2: 绘制的文本起始 x 坐标
- 参数3: 绘制的文本起始 y 坐标 文字的 baseline Y 坐标

关于 BaseLine:

![http://img.blog.csdn.net/20140402150646718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGFueWluZ2ppZTMyNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast](http://img.blog.csdn.net/20140402150646718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGFueWluZ2ppZTMyNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


# 获取文字的宽度

方式一：

```java
    /**
     * @param text  绘制的文字
     * @param paint 画笔
     * @return 文字的宽度
     */
    public int getTextWidth(String text, Paint paint) {
        Rect bounds = new Rect();
        paint.getTextBounds(text, 0, text.length(), bounds);
        int width = bounds.left + bounds.width();
        return width;
    }
```

方式二：

```java
    /**
     * @param text  绘制的文字
     * @param paint 画笔
     * @return 文字的宽度
     */
    public int getTextWidth2(String text, Paint paint) {
        return ((int) paint.measureText(text));
    }
```

# 获取文字的高度

```java
    /**
     * @param text  绘制的文字
     * @param paint 画笔
     * @return 文字的高度
     */
    public int getTextHeight(String text, Paint paint) {
        Rect bounds = new Rect();
        paint.getTextBounds(text, 0, text.length(), bounds);
        int height = bounds.bottom + bounds.height();
        return height;
    }
```

```java
-mPaint.ascent() + mPaint.descent()
```

# 文字的度量，参数获取 Paint.FontMetrics

```java
    mPaint.setStyle(Paint.Style.STROKE);
    canvas.drawRect(100, 100, 700, 300, mPaint);
    mPaint.setTextSize(100);    // 必须设置大小才能得到五条线的值
    Paint.FontMetrics metrics = mPaint.getFontMetrics(); // 基线
    float top = metrics.top;
    float ascent = metrics.ascent; // 五条线
    float leading = metrics.leading;
    float descent = metrics.descent;
    float bottom = metrics.bottom; // 最低底边的线
    Log.d(TAG, "top=" + top + "ascent=" + ascent + "leading=" + leading + "descent=" + descent + "bottom=" + bottom);
    canvas.drawText("测试文字", 100, 300, mPaint);  // 文字的基线 baseline    top,ascent,leading,descent,bottom
```

或者:

```java
float ascent = mPaint.ascent();
float descent = mPaint.descent();
float textHeight = -ascent + descent;
```

# 文本绘制水平居中

```java
        s
        # x
```

# 文本垂直居中

```java
        Paint.FontMetrics fontMetrics = mTextPaint.getFontMetrics();
        float y = getHeight() / 2 + (Math.abs(fontMetrics.ascent) - fontMetrics.descent) / 2;

        # y
```

# Paint 中和绘制文本相关的方法

- `mTextPaint.setStrikeThruText(true);` ：设置文本删除线
- `setTextSize(float f)`
- `setUnderlineText(booelan b)`: 设置下滑线

# 绘制透明文字

```java
mPaint.setAlpha(((int) (255 * progress)));
canvas.drawText(String.valueOf(number), 0, y + (ascent*(progress)), mPaint);
```


# 例子

## 文字交替动画

```java
        float ascent = mPaint.ascent();
        float descent = mPaint.descent();

        Paint.FontMetrics fontMetrics = mPaint.getFontMetrics();
        float y = getHeight() / 2 + (Math.abs(fontMetrics.ascent) - fontMetrics.descent) / 2;

        mPaint.setAlpha(((int) (255 * progress)));
        canvas.drawText(String.valueOf(oldNumber), 0, y - (ascent*(1-progress)), mPaint);
        mPaint.setAlpha(((int) (255 * (1 - progress))));
        canvas.drawText(String.valueOf(number), 0, y + (ascent*(progress)), mPaint);
```        
