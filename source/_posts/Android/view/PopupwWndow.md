---
title: PopupWindow 介绍
date: 2016-04-12 09:38:14
tags: PopupWindow
categories: android

---

# 获取 width hegith

```java

//UNSPECIFIED WRAP_CONTENT 的时候使用，其他的根据实际情况确定

mPopupWindow.getContentView().measure(View.MeasureSpec.UNSPECIFIED, View.MeasureSpec.UNSPECIFIED);

int popHeight=mPopupWindow.getContentView().getMeasuredHeight(); 
```


# PopupWindow 介绍
>一个弹出窗口控件，可以用来显示任意视图(View)，而且会浮动在当前 活动(activity)的顶部

## 使用

### 0 构造方法
>通过构造方法构建对象
有5个构造方法，介绍最终要的一个：

    public PopupWindow(View contentView, int width, int height, boolean focusable)

参数1：pupupWindow里面的布局控件；参数2：宽度；参数3：高度；参数4：是否可以获取焦点； 

### 1 显示popupWindow
#### showAsDropDown
- showAsDropDown(View anchor)
- showAsDropDown(View anchor, int xoff, int yoff)
- showAsDropDown(View anchor, int xoff, int yoff, int gravity)
- showAtLocation(View parent, int gravity, int x, int y)

### 其他的设置
#### 点击外部popup消失
    // =======  两者结合才能让popup点击外部消失
    popupWindow.setOutsideTouchable(true);
    popupWindow.setBackgroundDrawable(new BitmapDrawable());
    // =======  两者结合才能让popup点击外部消失

#### 焦点问题
popup具有处理触屏和物理按键的能力不是与生俱来的，需要设置 
    
    popupWindow.setFocusable(true);

#### popup的和软键盘
setSoftInputMode

#### 关于popup的动画
    public void setAnimationStyle(int animationStyle)


可以选用系统提供的

    popWindow.setAnimationStyle(android.R.style.Animation_InputMethod);
    可以使用自定义的进出动画：

android:windowEnterAnimation表示进入窗口动画
android:windowExitAnimation表示窗口退出动画


- 接口`PopupWindow.OnDismissListener`
	>当`PopupWindow`消失的时候调用

- 两个xml属性
	- `android:popupBackground`  --> `setBackgroundDrawable(Drawable)`
	- `android:popupElevation` --> `setElevation(float)`

- 常量：`setInputMethodMode(int)`
	- `INPUT_METHOD_FROM_FOCUSABLE`
	- `INPUT_METHOD_NEEDED`
	- `INPUT_METHOD_NOT_NEEDED`
- 构造方法 (n多)
	- `PopupWindow(View contentView, int width, int height)`：参数1是通过布局文件创建的`View`

几个重要的方法：

- 必须要**设置背景**否则无法响应点击事件
- `dismiss()`
- `setOutsideTouchable(true)`：设置后可以点击外面使它消失
- ` mPopupWindow.setHeight(ViewGroup.LayoutParams.WRAP_CONTENT);' 'mPopupWindow.setWidth(ViewGroup.LayoutParams.WRAP_CONTENT);`自适应宽高

- `setHeight` `setWidth`
- `setTouchInterceptor`这个方法会拦截屏幕上所有的点击事件

添加内部控件的点击事件和普通添加监听器的方式一样，获取控件`set..Listener..`



# PopupWindow 使用例子

```JAVA
    public void showMoreMenu() {
        mPopupWindowShowing = true;
        if (mPopupWindow == null) {
            View view = LayoutInflater.from(getActivity()).inflate(R.layout.teach_noti_more_menu, null);
            ViewGroup.LayoutParams layoutParams = view.getLayoutParams();

            mPopupWindow = new PopupWindow(view, ScreenUtils.dp2px(getActivity(), layoutParams.width),
                    layoutParams.height);
//        mPopupWindow.setFocusable(true);
            mPopupWindow.setTouchable(true);
            mPopupWindow.setBackgroundDrawable(new BitmapDrawable());
            mPopupWindow.setOutsideTouchable(true);

            view.findViewById(R.id.ll_teach_noti_all_read).setOnClickListener(v ->
                    mPresenter.setAllNotiReaded(mUserId));

            view.findViewById(R.id.ll_teach_noti_more_reward).setOnClickListener(v ->
                    showRewardUI());
        }
        mPopupWindow.showAsDropDown(mRightMore);
    }

```


