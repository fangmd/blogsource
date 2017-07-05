---
title: ViewAnimator (ViewFlipper)
date: 2016-01-14 12:18:12
tags: View
category: android

---


继承图：

![ViewAnimator继承图](https://github.com/fangmd/markdownphoto/raw/master/src/ViewAnimator继承图)


# ViewAnimator

## 介绍

>Base class for a FrameLayout container that will perform animations when switching between its views.

继承自 FrameLayout ，是用于展现 View 切换动画的基类


# ViewFlipper

## 介绍

>Simple ViewAnimator that will animate between two or more views that have been added to it. Only one child is shown at a time. If requested, can automatically flip between each child at a regular interval.

展示两个 View 切换的控件和 ViewPager 有点相似。

特点：

1. 一个界面只展示一个子 View
2. 可以实现 View 的自动切换

## 可设置 xml 属性

```xml
android:autoStart
android:flipInterval    
```

## Public Methods

- isAutoStart()
- isFlipping()
- setAutoStart(boolean autoStart)
- setFlipInterval(int milliseconds)
- startFlipping()
- stopFlipping()

## 基本使用

```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_views);

        mVF = (ViewFlipper) findViewById(R.id.vf_views);

        TextView textView;
        for (int i = 0; i < 4; i++) {
            textView = new TextView(this);
            textView.setTextSize(getResources().getDimensionPixelSize(R.dimen.text_size));
            textView.setText("Text" + i);
            textView.setBackgroundColor(Color.BLUE);
            textView.setGravity(Gravity.CENTER);
            mVF.addView(textView);
        }

        mVF.setOutAnimation(AnimationUtils.loadAnimation(this, R.anim.slide_out_top));
        mVF.setInAnimation(AnimationUtils.loadAnimation(this, R.anim.slide_in_bottom));
        mVF.setFlipInterval(1000);
    }

    public void stop(View view) {
        mVF.stopFlipping();
    }
    
    public void start(View view) {
        mVF.startFlipping();
    }
```

效果：

![ViewFlipper-Slide-Amination](https://github.com/fangmd/markdownphoto/raw/master/src/ViewFlipper.gif)







参考：

- [https://developer.android.com/reference/android/widget/ViewFlipper.html](https://developer.android.com/reference/android/widget/ViewFlipper.html)

