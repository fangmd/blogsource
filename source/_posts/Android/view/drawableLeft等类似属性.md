---
title: drawableLeft
date: 2016-04-14 21:38:14
tags: drawableLeft
categories: android

---


# drawableLeft等类似属性

转自：[http://bbs.csdn.net/topics/390383463](http://bbs.csdn.net/topics/390383463)

## code

	public void  setCompoundDrawables  (Drawable left, Drawable top, Drawable right, Drawable bottom);
	public void setCompoundDrawablesWithIntrinsicBounds (Drawable left,Drawable top, Drawable right, Drawable bottom)


。

    Drawable drawable = resources.getDrawable(drawablesDown[i]);
    drawable.setBounds(0, 0, px, px); // 做适配
    radioButton.setCompoundDrawables(null, drawable, null, null);

## xml

	android:drawableLeft="@drawable/icon"

