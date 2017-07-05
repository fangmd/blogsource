---
title: CardView
date: 2016-04-12 16:38:14
tags: CardView
categories: android

---


# CardView
<!--more-->

>和FrameLayout类似

## 导包
	
	compile 'com.android.support:cardview-v7:23.3.0'

## 版本区别
在5.0以下的手机和5.0版本以上的手机上显示效果会有点区别：5.0以上的手机card阴影可以覆盖后面的card，而5.0以下的版本会挤开旁边的cardview

## 属性
需要加入app命名空间

	app:cardCornerRadius="20dp"
	app:cardElevation="50dp"
	app:cardBackgroundColor="#00f"

## 点击事件

	card.setOnTouchListener(new View.OnTouchListener() {
		@Override
		public boolean onTouch(View v, MotionEvent event) {
			CardView cardView = (CardView) v;
			switch (event.getAction()) {
				case MotionEvent.ACTION_DOWN:
					cardView.setCardElevation(card.getMaxCardElevation());
				break;
				case MotionEvent.ACTION_UP:
					cardView.setCardElevation(card.getMaxCardElevation() / 2);
				break;
				case MotionEvent.ACTION_CANCEL:

				break;
			}
			return !v.isClickable();
		}
	});
	card.setOnClickListener(new View.OnClickListener() {
		@Override
		public void onClick(View v) {
			Toast.makeText(MainActivity.this, "点击了card1", Toast.LENGTH_SHORT).show();
			ObjectAnimator animator = ObjectAnimator.ofFloat(v, "radius", v.getWidth() / 2, 0);
        	animator.setDuration(2000);
        	animator.start();
		}
	});