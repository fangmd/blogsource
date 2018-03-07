---
title: view滑动实现方式 
date: 2016-04-04 22:18:12
tags: view
category: android

---

# view 滑动的实现方式

## view 位置

view在屏幕上的位置由一下参数决定：

- top：View的左上角的纵坐标y，`getTop()`,mTop
- left:View的左上角的横坐标x，`getLeft()`,mLeft
- bottom：View的右下角的纵坐标y，`getBottom()`,mBottom
- right:View的右下角的横坐标x，`getRight()`,mRight

<!--more-->

**注意：**

以上的坐标都是相对于父View来说的，坐标都是相对坐标，因为：子View的布局由父View完成

1. 更具以上的参数可以计算view宽高
2. 平移的参数：
	1. translationX： x = left + translationX;
	2. translationY： y = top + translationY;

这里的 xy 决定了 view 的最终位置；


## 方式一：`scrollTo/scrollBy`

>View自带的方法：scrollTo/scrollBy;

scrollTo：移动到某个位置；srcollBy:移动的位移量；

两个方法的源码：

    /**
     * Set the scrolled position of your view. This will cause a call to
     * {@link #onScrollChanged(int, int, int, int)} and the view will be
     * invalidated.
     * @param x the x position to scroll to
     * @param y the y position to scroll to
     */
    public void scrollTo(int x, int y) {
        if (mScrollX != x || mScrollY != y) {
            int oldX = mScrollX;
            int oldY = mScrollY;
            mScrollX = x;
            mScrollY = y;
            invalidateParentCaches();
            onScrollChanged(mScrollX, mScrollY, oldX, oldY);
            if (!awakenScrollBars()) {
                postInvalidateOnAnimation();
            }
        }
    }

    /**
     * Move the scrolled position of your view. This will cause a call to
     * {@link #onScrollChanged(int, int, int, int)} and the view will be
     * invalidated.
     * @param x the amount of pixels to scroll by horizontally
     * @param y the amount of pixels to scroll by vertically
     */
    public void scrollBy(int x, int y) {
        scrollTo(mScrollX + x, mScrollY + y);
    }

mScrollX: 表示view的左边缘 - view的内容的左边缘

mScrollY: 表示view的上边缘 - view的内容的上边缘

调用 scrollBy/scrollTo 方法只能实现View的内容的滚动，而View的四个位置参数是保持不变的。我们平常使用ListView时，滚动的是 ListView 的内容，而ListView本身在屏幕上的位置是不变的。向右滚动时mScrollX负的，向左滚动时mScrollX是正的。向下滚动时，mScrollY是负的，向上滚动时，mScrollY是正的。

滑动是非弹性的

## 方式二：动画

>通过改变view的 translationX 和 translationY 参数实现，好处是平滑；可以通过补间动画或属性动画实现平移；

### 补间动画：

1. 创建动画资源文件：

		<?xml version="1.0" encoding="utf-8"?>
		<set xmlns:android="http://schemas.android.com/apk/res/android"
		    android:fillAfter="true">
		
		    <translate
		        android:duration="100"
		        android:fromXDelta="0"
		        android:fromYDelta="0"
		        android:interpolator="@android:anim/linear_interpolator"
		        android:toXDelta="100"
		        android:toYDelta="100"/>
		
		</set>	
2. view.startAnimation(..)

补间动画移动的只是View的影像，View并未真正移动，比如button移动后它的可点击位置是不变的。

### 属性动画

	ObjectAnimator.ofFloat(targetView, "translationX" , 0, 100).setDuration(100).start;

## 方式三：改变布局参数

思路一：view右移就是把 marginLeft 参数增大，其他的同理

思路二：在要移动的 view 的旁边预先放置一个view，通过改变这个view的宽高改变目标view的位置

	MarginLayoutParams params = (MarginLayoutParams)mButton.getLayoutParams;
	params.leftMargin += 100;
	mButton.requestLayout;

非弹性。

## 使用 Scroller 实现弹性滑动

实现代码, 下面代码在 自定义 View 内使用

	Scroller scroller = new Scroller(mContext);
	
	private void smoothScrollTo(int dstX, int dstY) {
		int scrollX = getScrollX;
		int delta = dstX - scrollX;
		scroller.startScroll(scrollX, 0, delta, 0, 1000);
		invalidate();
	}
	 
	@Override
	public void computeScroll {
		if (scroller.computeScrollOffset) {
			scrollTo(scroller.getCurrX, scroller.getCurY);
			postInvalidate();
		}
	}

Scroller方法源码：

	public void startScroll(int startX, int startY, int dx, int dy, int duration) {  
		mMode = SCROLL_MODE;  
		mFinished = false;  
		mDuration = duration;  
		mStartTime = AnimationUtils.currentAnimationTimeMillis;  
		mStartX = startX;  
		mStartY = startY;  
		mFinalX = startX + dx;  
		mFinalY = startY + dy;  
		mDeltaX = dx;  
		mDeltaY = dy;  
		mDurationReciprocal = 1.0f / (float) mDuration;  
	    
		mViscousFluidScale = 8.0f;  
	
		mViscousFluidNormalize = 1.0f;  
		mViscousFluidNormalize = 1.0f / viscousFluid(1.0f);  
	}  

startScroll方法中并没有进行实际的滚动操作，而是把startX、startY、deltaX、deltaY等参数都保存了下来。

invalidate 方法，这个方法会请求重绘View，这会导致 View 的 draw 的方法被调用，draw 的方法内部会调用 computeScroll 方法。

调用了 scrollTo 方法，并传入 mScroller.getCurrX 和 mScroller.getCurrY 方法作为参数。

computeScrollOffset方法代码：

	public boolean computeScrollOffset {
		...
		int timePassed = (int) (AnimationUtils.currentAnimationTimeMillis - mStartTime);
		if (timePassed < mDuration) {
			switch (mMode) {
 			case SCROLL_MODE:
 			final float x = mInterpolator.getInterpolation(timePassed * mDurationReciprocal);
 			mCurrX = mStartX + Math.round(x * mDeltaX);
			mCurrY = mStartY + Math.rounc(y * mDeltaY);
			break;
			...
			}
		}
		return true;
	}

以上代码中第8行和第9行设置的 mCurrX 和 mCurrY 即为以上 scrollTo 的两个参数，表示本次滑动的目标位置。computeScrollOffset 方法返回 true 表示滑动过程还未结束，否则表示结束。

**原理：**invaldate方法会导致View的draw方法被调用，而draw会调用computeScroll方法，因此重写了computeScroll方法，而computeScrollOffset方法会根据时间的流逝动态的计算出很小的一段时间应该滑动多少距离。也就是把一次滑动拆分成无数次小距离滑动从而实现“弹性滑动”。




参考：《Android开发艺术探索》