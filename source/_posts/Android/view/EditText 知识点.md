---
title: EditText 知识点
date: 2016-04-24 11:38:14
tags: EditText
categories: android

---



# 设置限制输入

	android:digits="1234567890" <!--仅限输入数字-->

# 自动调出屏幕键盘

## 方案一：

	getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_STATE_VISIBLE);

关闭：

	getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_STATE_HIDDEN);

## 方案二：
>我使用的时候无效

	EditText yourEditText= (EditText) findViewById(R.id.yourEditText);
	InputMethodManager imm = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);
	imm.showSoftInput(yourEditText, InputMethodManager.SHOW_IMPLICIT);

关闭代码：

	InputMethodManager imm = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);
	imm.hideSoftInputFromWindow(yourEditText.getWindowToken(), 0);

<!--more-->

# 设置背景定制样式
覆盖掉默认的样式

	android:background="@color/white"

# EditText 调出键盘让底部的控件被键盘顶上来
## 方案一

只需要把底部需要不被键盘覆盖的控件用`ScrollView`包裹起来

	  <ScrollView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">
		。。。。。
	  </ScrollView>

## 方案二：
参考：

- [http://www.eoeandroid.com/thread-53414-1-1.html?_dsign=b3024058](http://www.eoeandroid.com/thread-53414-1-1.html?_dsign=b3024058)
- [http://my.oschina.net/gavinjin/blog/109369?fromerr=ZCIpS0zp](http://my.oschina.net/gavinjin/blog/109369?fromerr=ZCIpS0zp)



	getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE |
                WindowManager.LayoutParams.SOFT_INPUT_STATE_HIDDEN);

## 一个案例
![http://static.oschina.net/uploads/img/201302/20134425_3rSk.jpg](http://static.oschina.net/uploads/img/201302/20134425_3rSk.jpg)

A区域不动，B区域移动

1. 删除界面的全屏

2. Androidmanifest的activity设置成

		android:windowSoftInputMode="adjustResize|stateHidden"

例如:

		  <activity
		            android:name=".activitys.YTActivity"
		             android:windowSoftInputMode="adjustResize"
		            android:launchMode="singleTask" >
		        </activity>

# 取消焦点的获取

1. 在布局中加入一个不可见的View用于获取Focuse

	设置属性：focuseable=true

2. `editText.clearFocuse();`：清理焦点，让View获取到焦点

sedittext 焦点取消方法，view占位

# 防止自动获取焦点方法

在其父控件中添加下面的属性：

```JAVA
android:focusable="true"
android:focusableInTouchMode="true"
```

# 防止界面被软键盘挤压

在 Manifest 中设置 Activity 的属性：

```JAVA
android:windowSoftInputMode="adjustPan"
```


# 设置 action

```
actionUnspecified        未指定         EditorInfo.IME_ACTION_UNSPECIFIED.  
actionNone                 动作            EditorInfo.IME_ACTION_NONE 
actionGo                    去往            EditorInfo.IME_ACTION_GO
actionSearch               搜索            EditorInfo.IME_ACTION_SEARCH    
actionSend                 发送            EditorInfo.IME_ACTION_SEND   
actionNext                下一项           EditorInfo.IME_ACTION_NEXT   
actionDone               完成              EditorInfo.IME_ACTION_DONE 
```

监听事件：

```
mViewById = (EditText) findViewById(R.id.et);

mViewById.setOnKeyListener(new View.OnKeyListener() {
    @Override
    public boolean onKey(View view, int i, KeyEvent keyEvent) {

        if (i == EditorInfo.IME_ACTION_DONE) {

        }
		return false;
    }
});

