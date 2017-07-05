---
title: Configuration
date: 2016-04-18 12:18:12
tags: Configuration
category: android

---

# Configuration
>专门描述手机设备的配置信息

## 获得对象

	Configuration config = getResources().getConfiguration();

## 配置信息

1. fontScale:获取当前用户设置的字体的缩放因子。
2. keyboard：获取当前设备所关联的键盘类型。该属性的返回值：KEYBOARD_12KEY（只有12个键的小键盘）、KEYBOARD_NOKEYS、KEYBOARD_QWERTY（普通键盘）
3. keyboardHidden:该属性返回一个boolean值用于标识当前键盘是否可用。该属性不仅会判断系统的硬件键盘，也会判断系统的软键盘（位于屏幕）。
4. locale：获取用户当前的Locale.
5. mcc:获取移动信号的国家码
6. mnc:获取移动信号的网络码
7. navigation：判断系统上方向导航设备的类型。该属性的返回值：NAVIGATION_NONAV（无导航）、NAVIGATION_DPAD(DPAD导航）、NAVIGATION_TRACKBALL（轨迹球导航）、NAVIGATION_WHEEL（滚轮导航）
8. orientation：获取系统屏幕的方向。该属性的返回值：ORIENTATION_LANDSCAPE（横向屏幕）、ORIENTATION_PORTRAIT（竖向屏幕）
9. ouchscreen：获取系统触摸屏的触摸方式。该属性的返回值：TOUCHSCREEN_NOTOUCH（无触摸屏）、TOUCHSCREEN_STYLUS（触摸笔式触摸屏），TOUCHSCREEN_FINGER（接收手指的触摸屏）

##　监听系统设置改变
>测试了不行？？？？

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        String screan = newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE ? "横屏" : "竖屏";
        Toast.makeText(MainActivity.this,screan, Toast.LENGTH_SHORT).show();
    }
	







