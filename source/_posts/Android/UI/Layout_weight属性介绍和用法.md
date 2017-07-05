---
title: layout_weight属性介绍和用法
date: 2016-03-06 21:38:14
tags: 布局
categories: android

---

# layout_weight属性介绍和用法

## layout_weight意思
权重，一个控件尺寸占总尺寸的比例，这个尺寸可以是宽度也可以是长度视具体情况而定。

<!--more-->

## 控件尺寸计算方法
- 当LinearLayout布局的属性为：`android:orientation="horizontanl"`时：
	
	在子控件中设置`android:layout_weight`属性表示子控件宽度在**可分配宽度**上所占的比例。
- 当LinearLayout布局的属性为：`android:orientation="vertical"`时：

	在子控件中设置`android:layout_weight`属性表示子控件高度在**可分配高度**上所占的比例。

**可分配尺寸**计算方法：
	
	usable = `LinearLayout`所拥有的宽度(高度) - 子控件设置的宽度(高度)  
子控件从可分配尺寸中获得的尺寸：

	size = usable/(所有子控件weight值得和) * 该子控件的weight值
子控件最终获得的尺寸

	last = size + 控件设置的宽度(高度)属性的值

## 使用技巧
1. 在LinearLayout设置`android:weightSum`属性：可以在布局内留白

	![属性设置](http://i.imgur.com/f8sxuJr.png)
	![](http://i.imgur.com/3Nv1hEW.png)
2. 在LinearLayout设置`android:baselineAligned="false"`属性：保证子控件的对齐

	![属性设置前](http://i.imgur.com/xSRKwBC.png)
	![属性设置后](http://i.imgur.com/yAET7Hx.png)

	
