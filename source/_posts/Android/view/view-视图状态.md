---
title: View-视图状态
date: 2016-04-02 21:38:14
tags: Fragment
categories: android

---

# View-视图状态

## 状态

- `enabled`：表示视图是否可用，能否响应`onTouch`事件
- `focused`：表示视图是否可以获得焦点，可以使用`requestFocus()`方法让司徒获取焦点，返回`true`表示获取焦点成功；一般只有视图在focusable和focusable in touch mode同时成立的情况下才能成功获取焦点，比如说EditText。
- `window_focused`：表示视图是否处于正在交互的窗口，这个值由系统决定，应用程序无法改变它
- `selected`：表示视图是否处于选中状态，一个界面可以有多个视图处于选中状态，`setSelected(boolean)`设置值
- `pressed`：表示视图是否处于按下状态，`setPressed(boolean)`


## 状态改变
view状态改变的时候调用方法：`drawableStateChanged`

	protected void drawableStateChanged() {  
	    Drawable d = mBGDrawable;  
	        if (d != null && d.isStateful()) {  
		        d.setState(getDrawableState());  
		}  
	}

## 视图重绘
调用：`setVisibility(),setEnabled()..`都会导致视图重绘，内部都是调用了`invalidate()`方法实现的，我们也可以直接调用这个方法强制重绘视图。



参考:[http://blog.csdn.net/guolin_blog/article/details/17045157](http://blog.csdn.net/guolin_blog/article/details/17045157)
