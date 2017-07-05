---
title: 获取View宽高的方法
date: 2016-03-20 21:38:14
tags: view
categories: android

---

# 通过`View.post(..)`

		view.post(new Runnable(){
			@Override
			public void run(){
				int width = view.getMeasuredWidth();
				int height = view.getMeasuredHeight();
			}
		});

view的宽高需要在Measure过程后才能确定，直接在`onCreate`方法里面获取view的宽高只能得到0。上面方法在主线程的消息队列尾部插入一个消息保证其在`Measure`后执行。

# 通过`LayoutParams`获取
**在布局中控件的宽高设置为指定的值时有效**

	对于设置了宽高的View可以直接获取：view.getLayoutParams().height/width

# 手动Measure再获取

对于宽高设置具体数值的View和wrap_content的View可以通过以下方式：

		int widthMeasureSpec = MeasureSpec.makeMeasureSpec(100,MeasureSpec.EXACTLY);
		int heightMeasureSpec = MeasureSpec.makeMeasureSpec(100,MeasureSpec.EXACTLY);
		view.measure(widthMeasureSpec, heightMeasureSpec);
