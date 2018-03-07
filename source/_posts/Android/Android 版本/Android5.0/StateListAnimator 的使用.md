---
title: StateListAnimator 的使用
date: 2018-02-01 22:38:14
tags: [Android5.0, Animate]
categories: Android

---


[官方文档](https://developer.android.com/training/material/animations.html#ViewState)


<!--more-->


```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:state_pressed="true">
    <set>
      <objectAnimator
        android:duration="@android:integer/config_shortAnimTime"
        android:propertyName="scaleX"
        android:valueTo="1.025"
        android:valueType="floatType" />
      <objectAnimator
        android:duration="@android:integer/config_shortAnimTime"
        android:propertyName="scaleY"
        android:valueTo="1.025"
        android:valueType="floatType" />
      <objectAnimator
        android:duration="@android:integer/config_shortAnimTime"
        android:propertyName="translationZ"
        android:valueTo="4dp"
        android:valueType="floatType" />
    </set>
  </item>
  
  <item>
    <set>
      <objectAnimator
        android:duration="@android:integer/config_shortAnimTime"
        android:propertyName="scaleX"
        android:valueTo="1.0"
        android:valueType="floatType" />
      <objectAnimator
        android:duration="@android:integer/config_shortAnimTime"
        android:propertyName="scaleY"
        android:valueTo="1.0"
        android:valueType="floatType" />
      <objectAnimator
        android:duration="@android:integer/config_shortAnimTime"
        android:propertyName="translationZ"
        android:valueTo="0dp"
        android:valueType="floatType" />
    </set>
  </item>
  
</selector>
```

view xml 中添加属性：

```
 android:stateListAnimator="@drawable/style3"
```

view state 改变的时候改变属性。


# 用户按下的时候翻转 View

```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true">
        <set>
            <objectAnimator android:propertyName="rotationX"
                android:duration="@android:integer/config_shortAnimTime"
                android:valueTo="360"
                android:valueType="floatType"/>
        </set>
    </item>
    <item android:state_pressed="false">
        <set>
            <objectAnimator android:propertyName="rotationX"
                android:duration="@android:integer/config_shortAnimTime"
                android:valueTo="0"
                android:valueType="floatType"/>
        </set>
    </item>
</selector>  
```

参考：[http://blog.csdn.net/u010687392/article/details/48240181](http://blog.csdn.net/u010687392/article/details/48240181)
