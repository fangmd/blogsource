---
title: ConstraintLayout
date: 2018-11-12 21:38:14
tags: view
categories: android

---

# ConstraintLayout Deep Dive(Android Dev Summit '18)

>androidx.constraintlayout:constraintlayout:2.0.0-alpha2

- layout_width|layout_height 属性 `match_constraint` 表示填充剩余符合约束的剩余空间，在 xml 布局中使用 `0dp` 表示
- 设置控件宽高比 `app:layout_constraintDimensionRatio="1:1"`

当宽高比和以下约束冲突的时候，以下约束有更高的优先级。

```
app:layout_constraintBottom_toBottomOf="parent"
app:layout_constraintTop_toTopOf="parent"
app:layout_constraintLeft_toLeftOf="parent"
app:layout_constraintRight_toRightOf="parent"
```

一个控件对于另一个控件居中：

```
app:layout_constraintBottom_toBottomOf="@id/other_view"
app:layout_constraintTop_toTopOf="@id/other_view"
```

## 文本类控件布局

Baseline 对齐

```
app:layout_constraintBaseline_toBaselineOf=""
```




[ConstraintLayout Deep Dive](https://www.youtube.com/watch?v=P9Zstbk0lPw&list=PLWz5rJ2EKKc8WFYCR9esqGGY0vOZm2l6e&index=24)
