---
title: Android5.0 新动画
date: 2016-11-04 22:38:14
tags: [Android5.0]
categories: Android

---


# 点击波纹效果使用 touch feedback

## drawable-v21/touch_bg.xml

    <?xml version="1.0" encoding="utf-8"?>
    <ripple xmlns:android="http://schemas.android.com/apk/res/android"
            android:color="@color/line_grey">
        <item android:drawable="@color/white"/>
    </ripple>

<!--more-->

RippleDrawable 

## drawable/touch_bg.xml

    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@color/super_light_grey" android:state_pressed="true"/>
        <item android:drawable="@color/super_light_grey" android:state_focused="true"/>
        <item android:drawable="@color/white"/>
    </selector>

将上面的 drawable 设置为 background


# circular reveal 显示和隐藏控件的时候圆弧动画

show view：

```java
// previously invisible view
View myView = findViewById(R.id.my_view);

// get the center for the clipping circle
int cx = myView.getWidth() / 2;
int cy = myView.getHeight() / 2;

// get the final radius for the clipping circle
float finalRadius = (float) Math.hypot(cx, cy);

// create the animator for this view (the start radius is zero)
Animator anim =
    ViewAnimationUtils.createCircularReveal(myView, cx, cy, 0, finalRadius);

// make the view visible and start the animation
myView.setVisibility(View.VISIBLE);
anim.start();
```

hide view:

```java
// previously visible view
final View myView = findViewById(R.id.my_view);

// get the center for the clipping circle
int cx = myView.getWidth() / 2;
int cy = myView.getHeight() / 2;

// get the initial radius for the clipping circle
float initialRadius = (float) Math.hypot(cx, cy);

// create the animation (the final radius is zero)
Animator anim =
    ViewAnimationUtils.createCircularReveal(myView, cx, cy, initialRadius, 0);

// make the view invisible when the animation is done
anim.addListener(new AnimatorListenerAdapter() {
    @Override
    public void onAnimationEnd(Animator animation) {
        super.onAnimationEnd(animation);
        myView.setVisibility(View.INVISIBLE);
    }
});

// start the animation
anim.start();
```


# activity transition 跳转动画


# curved motion 弯曲动画


# Animate changes in one or more view properties with view state change animations 控价属性改变动画


# Show animations in state list drawables between view state changes


# Animate Vector Drawables 矢量图动画



