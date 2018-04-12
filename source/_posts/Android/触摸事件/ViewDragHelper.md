---
title: ViewDragHelper
date: 2016-11-16 13:18:12
tags: [自定义View]
category: android

---

用于分析手势处理的类,一般用在一个自定义 ViewGroup 的内部

ViewDragHelper.Callback 是连接 ViewDragHelper 与 view 之间的桥梁（这个 view 一般是指拥子 view 的容器即 parentView）；

   ViewDragHelper的实例是创建的；

   ViewDragHelper 并不是直接作用于要被拖动的View，而是使其控制的视图容器中的子 View 可以被拖动，如果要指定某个子 view 的行为，需要在 Callback 中想办法；

   ViewDragHelper 的本质其实是分析 onInterceptTouchEvent 和onTouchEvent 的 MotionEvent 参数，然后根据分析的结果去改变一个容器中被拖动子 View 的位置（ 通过 offsetTopAndBottom(int offset)和offsetLeftAndRight(int offset)方法 ），他能在触摸的时候判断当前拖动的是哪个子 View；

# ViewDragHelper 类

创建 ViewDragHelper 对象的方法：（通过静态工厂方法）

	public static ViewDragHelper create(ViewGroup forParent, Callback cb)

	public static ViewDragHelper create(ViewGroup forParent, float sensitivity, Callback cb)

参数：

1. ViewGroup：ViewDragHelper 作用于的控件组
2. sensitivity：灵敏度，最大越灵敏 1.0f为正常值
3. Callback: 事件回调的接口


# 使用

## 创建 ViewDragHelper




## 让 ViewDragHelper 接管 ViewGroup 的触摸事件

	@Override
	public boolean onInterceptTouchEvent(MotionEvent ev) {
	  final int action = MotionEventCompat.getActionMasked(ev);
	  if (action == MotionEvent.ACTION_CANCEL || action == MotionEvent.ACTION_UP) {
	      mDragHelper.cancel();
	      return false;
	  }
	  return mDragHelper.shouldInterceptTouchEvent(ev);
	}
	@Override
	public boolean onTouchEvent(MotionEvent ev) {
	  mDragHelper.processTouchEvent(ev);
	  return true;
	}

然后就可以在回调中处理事件了

## 触摸事件处理

### 拖动行为的处理
#### 处理横向的拖动

在 Callback 中实现下面的方法：

	@Override
	public int clampViewPositionHorizontal(View child, int left, int dx) {
	  Log.d("DragLayout", "clampViewPositionHorizontal " + left + "," + dx);
	  final int leftBound = getPaddingLeft();
	  final int rightBound = getWidth() - mDragView.getWidth();
	  // 为了让被拖动的view遇到边界之后就不在拖动，对返回的值做了更多的考虑。
	  final int newLeft = Math.min(Math.max(left, leftBound), rightBound);
	  return newLeft;
	  // return left; //就能实现移动
	}

参数：

1. View：表示当前触摸作用于的子 View
2. left：当前子 View 的 x 坐标
3. dx: 触摸点在 x 轴上做出的移动距离

返回值：表示子 View 新的 x 坐标

#### 处理纵向的拖动

	@Override
	public int clampViewPositionVertical(View child, int top, int dy) {
	  final int topBound = getPaddingTop();
	  final int bottomBound = getHeight() - mDragView.getHeight();
	  final int newTop = Math.min(Math.max(top, topBound), bottomBound);
	  return newTop;
	}

## 滑动边缘

滑动边缘： EDGE_LEFT ,EDGE_TOP, EDGE_RIGHT, EDGE_BOTTOM

设置边缘：

	mDragHelper.setEdgeTrackingEnabled(ViewDragHelper.EDGE_LEFT);

实现方法 Callback：

	@Override
	public void onEdgeTouched(int edgeFlags, int pointerId) {
	    super.onEdgeTouched(edgeFlags, pointerId);
	    Toast.makeText(getContext(), "edgeTouched", Toast.LENGTH_SHORT).show();
	}

如果你想在边缘滑动的时候根据滑动距离移动一个子view，可以通过实现onEdgeDragStarted方法，并在onEdgeDragStarted方法中手动指定要移动的子View:

	@Override
	public void onEdgeDragStarted(int edgeFlags, int pointerId) {
	    mDragHelper.captureChildView(mDragView2, pointerId);
	}

参数：

1. edgeFlags：滑动的边缘
2. pointerId：本次触摸事件的ID

## 加速度检测


## 回调Drag Release

Callback: 手指抬起回调

	@Override
	public void onViewReleased(View releasedChild, float xvel, float yvel) {
		//...
	}

### 实现 View 返回动画

Callback：

	@Override
	public void onViewReleased(View releasedChild, float xvel, float yvel) {

		//mAutoBackView手指释放时可以自动回去
		if (releasedChild == mAutoBackView) {
			mViewDragHelper.settleCapturedViewAt(mAutoBackOriginPos.x, mAutoBackOriginPos.y);
			invalidate();
		}
	}

CustomView：

	@Override
	public void computeScroll()
	{
		if(mViewDragHelper.continueSettling(true))
		{
			invalidate();
		}
	}

View 滑动返回动画内部使用的是： mScroller.startScroll。

## 移动到某个指定的位置

	mViewDragHelper.settleCapturedViewAt(mAutoBackOriginPos.x, mAutoBackOriginPos.y);

## 设置触摸处理的开关
>这种方法也可以用于滑动冲突的解决

在自定义 View 中自定义一个方法：

	public void setDragAble(boolean b) {
		mDragAble = b;
	//        if (b) {
	//
	//        } else {
	//            mViewDragHelper.abort();
	//        }
	}

在 Callback 中：

	@Override
	public boolean tryCaptureView(View child, int pointerId) {
		//.....
		return mDragAble;
	}

参考：

- [http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/0911/1680.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/0911/1680.html)
- [http://blog.csdn.net/lmj623565791/article/details/46858663](http://blog.csdn.net/lmj623565791/article/details/46858663)
