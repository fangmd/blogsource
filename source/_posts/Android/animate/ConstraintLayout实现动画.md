---
title: ConstraintLayout 实现动画
date: 2017-12-21 21:38:14
tags: Animation
category: android

---

# 方式二 设置属性


```java

mConstraintSet1 = new ConstraintSet();
mConstraintSet1.clone(mRoot);
mConstraintSet2 = new ConstraintSet();
mConstraintSet2.clone(this, R.layout.activity_constaint_layout_animate_small);

public void change2(View view) {
    TransitionManager.beginDelayedTransition(mRoot);
    if (mChanged) {
        mConstraintSet1.setMargin(R.id.view, ConstraintSet.START, 0);
        mConstraintSet1.constrainWidth(R.id.view, mWidthMax);
        mConstraintSet1.setMargin(R.id.view, ConstraintSet.TOP, 0);
        mConstraintSet1.applyTo(mRoot);
    } else {
        int margin = (mWidthMax - mWidthMin) / 2;
        Log.d(TAG, "change: " + margin);
        mConstraintSet1.constrainWidth(R.id.view, mWidthMin);
        mConstraintSet1.setMargin(R.id.view, ConstraintSet.LEFT, margin);
        mConstraintSet1.setMargin(R.id.view, ConstraintSet.TOP, 200);
        mConstraintSet1.applyTo(mRoot);
    }
    mChanged = !mChanged;
}
```


# 方式一 创建两个布局

>两个布局的控件 id 要一样， constraint 的约束属性要齐全

`activity_constaint_layout_animate.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/c_animate_root"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#a1a1a1"
    tools:context="com.fangmingdong.androiddemo.constainslayoutAnimate.ConstraintLayoutAnimateActivity">

    <View
        android:id="@+id/view"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:background="#a0ffaa"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <Button
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:onClick="change"
        android:text="Change"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"/>

</android.support.constraint.ConstraintLayout>
```



`activity_constaint_layout_animate_small.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/c_animate_root"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#a1a1a1"
    tools:context="com.fangmingdong.androiddemo.constainslayoutAnimate.ConstraintLayoutAnimateActivity">

    <View
        android:id="@+id/view"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:layout_marginLeft="20dp"
        android:layout_marginRight="20dp"
        android:layout_marginTop="20dp"
        android:background="#a0ffaa"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <Button
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:onClick="change"
        android:text="Change"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"/>

</android.support.constraint.ConstraintLayout>
```

使用：

```java
mConstraintSet1 = new ConstraintSet();
mConstraintSet1.clone(mRoot);
mConstraintSet2 = new ConstraintSet();
mConstraintSet2.clone(this, R.layout.activity_constaint_layout_animate_small);

public void change(View view) {
    TransitionManager.beginDelayedTransition(mRoot);
    if (mChanged) {
        mConstraintSet2.applyTo(mRoot);
    } else {
        mConstraintSet1.applyTo(mRoot);
    }
    mChanged = !mChanged;
}
```


