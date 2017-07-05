---
title: Android 悬浮窗实现
date: 2016-04-10 11:38:14
tags: android悬浮窗
categories: android

---

# Android 悬浮窗实现
>在不需要权限的情况下使用悬浮窗

## 1 权限
>使用toast类型窗口不用设置权限

	<!-- 显示顶层浮窗 -->
	<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />

<!--more-->
## 2 服务获取

	// 获取应用的Context
	mContext = context.getApplicationContext();
	// 获取WindowManager
	mWindowManager = (WindowManager) mContext.getSystemService(Context.WINDOW_SERVICE);

## 3 参数设置

	WindowManager.LayoutParams params = new WindowManager.LayoutParams();

### 3.1 悬浮窗类型

	params.type = WindowManager.LayoutParams.TYPE_TOAST; // 使用这个窗口可以不用权限
>TYPE_TOAST一直都可以显示, 但是用TYPE_TOAST显示出来的在2.3上无法接收点击事件, 因此还是无法随意使用.
下面是我之前研究后台线程显示对话框的时候记得笔记, 大家可以看看我们项目中有需求需要在后台任务中显示Dialog, 项目最初的做法是用Activity模拟Dialog, 一个Activity已经承载了近20种Dialog, 代码混乱至极. 后来我发现Dialog可以通过改变Window Type实现不依赖Activity显示, 然后就很兴奋的要在使用这种方式来作为新的实现方式.
最初WindowType是WindowManager.LayoutParams.TYPE_SYSTEM_ALERT, 可是这是悬浮窗了, MIUI会默认禁止(真他妈操蛋，也没有任何提示)最终放弃. 后来试着换成了WindowManager.LayoutParams.TYPE_TOAST, 起初效果很好，MIUI也不禁止了, 哪里都能显示, 这下开心了. 可是后来又发现在2.3上不能接收点击事件, 也就是说Dialog上的按钮不能点击, 这他妈就很操蛋了, 又放弃了. 又试了试其他的Type都不能满足需求, 结果如下：

>TYPE_SEARCH_BAR: 未知
>
>TYPE_ACCESSIBILITY_OVERLAY: 拒绝使用

>TYPE_APPLICATION: 只能配合Activity在当前APP使用TYPE_APPLICATION_ATTACHED_DIALOG: 只能配合Activity在当前APP使用
>
>TYPE_APPLICATION_MEDIA: 无法使用(什么也不显示)
>
>TYPE_APPLICATION_PANEL: 只能配合Activity在当前APP使用(PopupWindow默认就是这个Type)
>
>TYPE_APPLICATION_STARTING: 无法使用(什么也不显示)
>
>TYPE_APPLICATION_SUB_PANEL: 只能配合Activity在当前APP使用TYPE_BASE_APPLICATION: 无法使用(什么也不显示)
>TYPE_CHANGED: 只能配合Activity在当前APP使用
>
>TYPE_INPUT_METHOD: 无法使用(直接崩溃)
>
>TYPE_INPUT_METHOD_DIALOG: 无法使用(直接崩溃)
>
>TYPE_KEYGUARD_DIALOG: 拒绝使用
>
>TYPE_PHONE: 属于悬浮窗(并且给一个Activity的话按下HOME键会出现看不到桌面上的图标异常情况)
>TYPE_TOAST: 不属于悬浮窗, 但有悬浮窗的功能, 缺点是在Android2.3上无法接收点击事件
TYPE_SYSTEM_ALERT: 属于悬浮窗, 但是会被禁止

>文／Shawon（简书作者）
原文链接：http://www.jianshu.com/p/167fd5f47d5c
著作权归作者所有，转载请联系作者获得授权，并标注“简书作者”。

### 3.2 设置flag

	int flags = WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM
                | WindowManager.LayoutParams.FLAG_NOT_TOUCH_MODAL
                | WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE;

	params.flags = flags;

	// 不设置这个弹出框的透明遮罩显示为黑色
	params.format = PixelFormat.TRANSLUCENT;


- FLAG_NOT_TOUCH_MODAL 不阻塞事件传递到后面的窗口
- FLAG_NOT_FOCUSABLE 悬浮窗口较小时，后面的应用图标由不可长按变为可长按，不设置这个flag的话，home页的划屏会有问题
- FLAG_NOT_FOCUSABLE，窗口收不到Back键的事件,back点击事件就会由后面的view处理

### 3.3 宽高

	params.width = WindowManager.LayoutParams.WRAP_CONTENT;
	params.height = WindowManager.LayoutParams.WRAP_CONTENT;
	params.gravity = Gravity.CENTER;

## 4 悬浮窗添加view

	mWindowManager.addView(mView, params);
		
### 4.1 mView代码

	View view = LayoutInflater.from(mContext).inflate(R.layout.popup_window, null);
	// TODO:点击事件
	ImageButton imgBtn = (ImageButton) view.findViewById(R.id.main_bottom_play);
        mindecateImg = ((ImageView) view.findViewById(R.id.main_bottom_play_play));
	imgBtn.setOnClickListener(new View.OnClickListener() {
		@Override
		public void onClick(View v) {
			Log.d(TAG, "onClick: popwindow点击");
			if (mService != null) {
				mService.playOrPause(mService.getCurrPos());
				if (mService != null) {
					mService.playOrPause(mService.getCurrPos());
				}
			}
		}
	});


参考：

1. [http://www.jianshu.com/p/634cd056b90c](http://www.jianshu.com/p/634cd056b90c)
2. [http://www.liaohuqiu.net/cn/posts/android-windows-manager/](http://www.liaohuqiu.net/cn/posts/android-windows-manager/)
3. [http://www.cnblogs.com/mengdd/p/3824782.html](http://www.cnblogs.com/mengdd/p/3824782.html)
4. [Android GWES之Android窗口管理](http://blog.csdn.net/maxleng/article/details/5557758)