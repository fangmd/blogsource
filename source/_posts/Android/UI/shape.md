---
title: shape 
date: 2016-04-03 13:18:12
tags: [shape,drawable]
category: android

---

# Shape 使用

## shape层叠应用

	<?xml version="1.0" encoding="utf-8"?>
	<selector xmlns:android="http://schemas.android.com/apk/res/android" xmlns:tools="http://schemas.android.com/tools">
	    //当drawable状态为checked时显示的图形
	    <item android:state_checked="true" >
	        <layer-list>//表示使用图形层级
	            //第一层（底部蓝色）
	            <item>
	                <shape android:shape="oval">//绘制图形，oval=圆形
	                    <solid android:color="@android:color/holo_blue_light" />//载入系统holo主题的浅蓝色
	                    <size android:width="20dp" android:height="20dp" />//图形的高宽
	                </shape>
	            </item>
	            //第二层（面层白色)，并向内偏移2dp
	            <item android:bottom="2dp" android:left="2dp" android:right="2sp" android:top="2dp">
	                <shape android:shape="oval">//绘制图形，oval=圆形
	                    <stroke android:width="2dp" android:color="@android:color/white" />//白色环形带，宽度2dp
	                    <size android:width="20dp" android:height="20dp" />//图形的高宽
	                </shape>
	            </item>
	        </layer-list>
	    </item>
	
	    //当drawable状态为非checked时显示的图形
	    <item android:state_checked="false" >
	        <layer-list>
	            <item>
	                <shape android:shape="oval">
	                    <solid android:color="@android:color/holo_blue_light" />
	                    <size android:width="20dp" android:height="20dp" />
	                </shape>
	            </item>
	        </layer-list>
	    </item>
	</selector>

注意点：状态属性不能设置多个(selected,checked)？？
	


参考：
- [http://www.jianshu.com/p/30a85a26466f](http://www.jianshu.com/p/30a85a26466f)
