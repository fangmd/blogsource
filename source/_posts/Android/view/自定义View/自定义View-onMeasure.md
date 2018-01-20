---
title: 自定义View -- onMeasure
date: 2018-01-19 09:23:14
tags: [自定义View]
categories: Android

---


- 重写onMeasure()方法是为了自定义View尺寸的规则
- 如果你的自定义View的尺寸是根据父控件行为一致，就不需要重写onMeasure()方法



```java
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    super.onMeasure(widthMeasureSpec, heightMeasureSpec);
}
```

- `widthMeasureSpec`, `heightMeasureSpec`: 里面封装了 size 和 mode


# MeasureSpec.class

解封 `onMeasure(int widthMeasureSpec, int heightMeasureSpec)` 方法中的两个参数

```java
int specMode = MeasureSpec.getMode(heightMeasureSpec);
int specSize = MeasureSpec.getSize(heightMeasureSpec);
```


Mode 类型有：

```
        /**
         * Measure specification mode: The parent has not imposed any constraint
         * on the child. It can be whatever size it wants.
         */
        public static final int UNSPECIFIED = 0 << MODE_SHIFT;

        /**
         * Measure specification mode: The parent has determined an exact size
         * for the child. The child is going to be given those bounds regardless
         * of how big it wants to be.
         */
        public static final int EXACTLY     = 1 << MODE_SHIFT;

        /**
         * Measure specification mode: The child can be as large as it wants up
         * to the specified size.
         */
        public static final int AT_MOST     = 2 << MODE_SHIFT;
```        


# 当不实现 onMeasure 方法，或者实现了直接调用父类 onMeasure 方法的时候

默认大小获取方法：

```java
public static int getDefaultSize(int size, int measureSpec) {
    int result = size;
    int specMode = MeasureSpec.getMode(measureSpec);
    int specSize = MeasureSpec.getSize(measureSpec);

    switch (specMode) {
    case MeasureSpec.UNSPECIFIED:
        result = size;
        break;
    case MeasureSpec.AT_MOST:
    case MeasureSpec.EXACTLY:
        result = specSize;
        break;
    }
    return result;
}
```

- 自定义View 的 layout_width 以及 layout_height 属性值 match_parent 或者 wrap_content 显示大小由其父容器控件决定。
- 自定义View 设置为固定的值，就显示该设定的值

# MeasureSpec Mode

MeasureSpec.UNSPECIFIED：

MeasureSpec.AT_MOST: 子控件设置成 wrap_content 的时候

MeasureSpec.EXACTLY: 子控件设置指定的值，或者设置成 match_parent 的时候触发

一个自定义 View 例子 高度计算例子：

```java
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);

        int width = measureWidth(widthMeasureSpec);
        int height = measureHeight(heightMeasureSpec);

        setMeasuredDimension(width, height);
    }

    private int measureHeight(int heightMeasureSpec) {
        int ret = 0;
        int specMode = MeasureSpec.getMode(heightMeasureSpec);
        int specSize = MeasureSpec.getSize(heightMeasureSpec);

        switch (specMode) {
            case MeasureSpec.EXACTLY:   // 子控件设置了指定的大小
                ret = MeasureSpec.getSize(heightMeasureSpec);
                break;
            case MeasureSpec.AT_MOST:   //
                ret = (int) (mPaint.measureText(String.valueOf(mText)) + getPaddingLeft() + getPaddingRight());
                ret = Math.min(ret, specSize);
                break;
            case MeasureSpec.UNSPECIFIED:
                ret = (int) (mPaint.measureText(String.valueOf(mText)) + getPaddingLeft() + getPaddingRight());
                break;
        }

        return ret;
    }
```



