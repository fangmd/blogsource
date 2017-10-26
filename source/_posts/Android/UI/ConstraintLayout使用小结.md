---
title: ConstraintLayout 使用小结
date: 2017-09-19 12:18:12
tags: [Layout]
category: android

---

# 0dp

在 ConstraintLayout 中控价的 `0dp` 也叫 `match_constraint`，默认行为是撑开(spread)，占满剩余的所有空间。

## 配合 percent 实现占有剩余空间的百分比

```
    <TextView
        android:id="@+id/textView6"
        android:layout_width="0dp"
        android:layout_height="0dp"
        
        app:layout_constraintHeight_default="percent"
        app:layout_constraintHeight_percent="0.5"
        
        app:layout_constraintWidth_default="percent"
        app:layout_constraintWidth_percent="0.5" />
```


# 使用 ConstraintLayout 代替 RelativeLayout

两者之间相似的属性对应表：

CL属性|RL属性
---|---
layout_constraintLeft_toLeftOf|layout_alignLeft
layout_constraintLeft_toRightOf|layout_toRightOf
layout_constraintRight_toLeftOf|layout_toLeftOf
layout_constraintRight_toRightOf|layout_alignRight
layout_constraintTop_toTopOf|layout_alignTop
layout_constraintTop_toBottomOf|layout_below
layout_constraintBottom_toTopOf|layout_above
layout_constraintBottom_toBottomOf|layout_alignBottom
layout_constraintBaseline_toBaselineOf|layout_alignBaseline
layout_constraintStart_toEndOf|layout_toEndOf (API 17)
layout_constraintStart_toStartOf|layout_alignStart (API 17)
layout_constraintEnd_toStartOf|layout_toStartOf (API 17)
layout_constraintEnd_toEndOf|layout_alignEnd (API 17)

在 ConstraintLayout 用一个 **id=parent 的对象表示当前控件的父控件**

## 实现空间相对父空间居中 (layout_centerInParent=”true”)

```
app:layout_constraintLeft_toLeftOf="parent"
app:layout_constraintRight_toRightOf="parent"
app:layout_constraintTop_toTopOf="parent"
app:layout_constraintBottom_toBottomOf="parent"
```

同时需要设置控件的固定宽高或者 `wrap_content`

>当 View 设置成 `wrap_content` 的时候，View 和其父控件一样大，于设置成 `match_parent` ,`0dp` 的结果一样; TextView 设置成 `wrap_content` 正常。


居中实现方法2:

```
app:layout_constraintLeft_toLeftOf="parent"
app:layout_constraintRight_toRightOf="parent"
app:layout_constraintHorizontal_bias="0.5"
app:layout_constraintTop_toTopOf="parent"
app:layout_constraintBottom_toBottomOf="parent"
app:layout_constraintVertical_bias="0.5"
```


# 使用 ConstraintLayout 代替 LinearLayout

## 加权链 实现 `weigth`

链的默认行为是在可用空间中平均分配位置。 如果一个或多个元素使用MATCH_CONSTRAINT，它们将使用剩余的空白空间（在它们之间相等）

属性 `layout_constraintHorizontal_weight`, `layout_constraintVertical_weight` 定义控件在剩余空间中能分到的位置。


### demo

```
    <View
        android:id="@+id/chain_view_0"
        android:layout_width="40dp"
        android:background="@android:color/holo_green_dark"
        android:layout_height="40dp"

        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/chain_view_1"
        app:layout_constraintHorizontal_chainStyle="spread"
        app:layout_constraintHorizontal_weight="2"
        />

    <View
        android:id="@+id/chain_view_1"
        android:layout_width="0dp"
        android:background="@color/colorPrimary"
        android:layout_height="40dp"

        app:layout_constraintLeft_toRightOf="@id/chain_view_0"
        app:layout_constraintRight_toLeftOf="@+id/chain_view_2"
        app:layout_constraintHorizontal_weight="1"/>


    <View
        android:id="@+id/chain_view_2"
        android:layout_width="0dp"
        android:background="@android:color/holo_purple"
        android:layout_height="40dp"

        app:layout_constraintLeft_toRightOf="@+id/chain_view_1"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintHorizontal_weight="1"/>
```

![chain_weight](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/ConstrainsLayout/chain_weight.png)






## bias

通过 layout_constraintHorizontal_bias 和 layout_constraintVertical_bias 两个属性，可以简单直观的完成间距比例的设置。



# 设置控件 固定比例宽高

例子：

```
<!--layout_constraintDimensionRatio的H/W表示被动调整的是高(H)或是宽(W)-->

app:layout_constraintDimensionRatio="H,4:3"
```

按照宽高比4:3展示，宽为固定边，高为被动边

# GoneMargin

```
layout_goneMarginStart
layout_goneMarginEnd
layout_goneMarginLeft
layout_goneMarginTop
layout_goneMarginRight
layout_goneMarginBottom
```

其中一方Visibility == Gone时，另外一方将会根据layout_goneMargin系列属性的值重新规划自己的间距。


# chain


chain 是 ConstraintLayout 中加入的控件与控件间的关系。组成 chain 的多个控件，可以在同一方向上更加方便的完成复杂的布局要求。


## 组成 chain

两个控件组成 chain 需要满足的条件：

1. 控件间的布局存在相互依赖关系（你依赖我布局，我也依赖你布局）
2. 两个以上的控件，相互依赖关系需要保持在同一个方向上（都是水平方向上的依赖：Left_toRightOf / Right_toLeftOf；或者都是垂直方向上的依赖：Top_toBottomOf / Bottom_toTopOf）

## 设置 chain style

![chain style](https://mmbiz.qpic.cn/mmbiz_png/6GYDCe0a268PLcpvXmbU3LncJiaZMiaDpJdeByyMzFfas5KE2SjBCITdPBztRz2aSVFkR3URBicMAh3hpftALA4tQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### demo 1 chain style 

```
    <View
        android:id="@+id/chain_view_0"
        android:layout_width="40dp"
        android:background="@android:color/holo_green_dark"
        android:layout_height="40dp"

        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/chain_view_1"
        app:layout_constraintHorizontal_chainStyle="spread"/>

    <View
        android:id="@+id/chain_view_1"
        android:layout_width="40dp"
        android:background="@color/colorPrimary"
        android:layout_height="40dp"

        app:layout_constraintLeft_toRightOf="@id/chain_view_0"
        app:layout_constraintRight_toLeftOf="@+id/chain_view_2"/>


    <View
        android:id="@+id/chain_view_2"
        android:layout_width="40dp"
        android:background="@android:color/holo_purple"
        android:layout_height="40dp"

        app:layout_constraintLeft_toRightOf="@+id/chain_view_1"
        app:layout_constraintRight_toRightOf="parent"/>
```


![chain_style_spread](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/ConstrainsLayout/chain_style_spread.png)


![chain_style_spread_inside](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/ConstrainsLayout/chain_style_spread_inside.png)


![chain_style_spread_packed](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/ConstrainsLayout/chain_style_packed.png)







参考：

- [https://mp.weixin.qq.com/s/X01KpEbegR47Qnl9TmUd5w](https://mp.weixin.qq.com/s/X01KpEbegR47Qnl9TmUd5w)