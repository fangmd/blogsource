---
title: android5.0 波纹效果
date: 2016-11-04 12:18:12
tags: [android5.0, 波纹效果]
category: android

---

android5.0后支持

# 例子

## drawable-v21/touch_bg.xml

	<?xml version="1.0" encoding="utf-8"?>
	<ripple xmlns:android="http://schemas.android.com/apk/res/android"
	        android:color="@color/line_grey">
	    <item android:drawable="@color/white"/>
	</ripple>

<!--more-->

## drawable/touch_bg.xml

	<?xml version="1.0" encoding="utf-8"?>
	<selector xmlns:android="http://schemas.android.com/apk/res/android">
	    <item android:drawable="@color/super_light_grey" android:state_pressed="true"/>
	    <item android:drawable="@color/super_light_grey" android:state_focused="true"/>
	    <item android:drawable="@color/white"/>
	</selector>

将上面的 drawable 设置为 background
