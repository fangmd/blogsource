---
title: 自定义View -- 自定义属性
date: 2016-12-28 09:23:14
tags: [View, 自定义View, 自定义属性]
categories: Android

---


## 创建按照正常步骤创建 自定义View

## 在 values/attrs.xml 中创建自定义属性

例子：

```xml
    <declare-styleable name="MyView">
        <attr name="mv_center_color" format="color"/>

    </declare-styleable>
```

Note：

- 属性命名添加前缀防止重复

## 在布局中使用自定义属性

例子：

需要在跟布局中加入命名空间：`xmlns:app="http://schemas.android.com/apk/res-auto"`

```xml
    <com.waytone.animationdemo.indicate.MyView
        android:id="@+id/status_indicate_view"
        android:layout_width="100dp"
        app:siv_center_color="#f23"
        app:siv_text_size="12"
        android:layout_height="100dp"/>
```

## 在 自定义View 内部解析属性

例子：

```java
        TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.StatusIndicateView);
        int color = a.getColor(R.styleable.StatusIndicateView_siv_center_color, Color.BLACK);
        int testSize = a.getInteger(R.styleable.StatusIndicateView_siv_text_size, 0);
        mCenterPaint.setColor(color);

        a.recycle();
```