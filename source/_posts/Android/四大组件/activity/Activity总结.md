---
title: Activity总结
date: 2016-05-07 12:18:12
tags: Activity
category: android

---


# Activity总结

## 官方文档

###　Class Overview

1. activity用于展示界面，和用户交互；`setContentView(view)`方法加载布局
2. activity非全局展示： 1. 主题设置中添加`windowIsFloating` 2. 使用`ActivityGroup`方式展示activity

3. `onPause()`方法中需要保存`Acitivity`中对数据的改变，或者使用`ContentProvider`保存数据


### Activity Lifecycle

#### 两张图了解Activity生命周期
![activity-lifecycle](http://upload-images.jianshu.io/upload_images/2063726-fcef5c9641181916.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![activity-lifecycle](http://img.blog.csdn.net/20150926092715673)

1. activities在系统启动都会被`activity stack`管理，

生命周期简单介绍：

1. `onCreate()`:在activity创建的时候最先执行,一般会执行一些初始化的操作比如:创建视图,初始化控件绑定数据,同时方法中会传入一个Bundle对象用于现场保护.
2. `onStart()`:
3. `onResume()`
4. `onPause()`
5. `onStop()`-->`onRestart()`-->`onStart()`
6. `onDestory()`：activity调用`finishing()`或者被系统销毁了
7. `onRestart()`

<!--more-->
三种状态：

1. entire lifetime: `onCreate(Bundle)` --> `onDestory()`
2. visible lifetime: `onStart()` --> `onStop()`,例子：`BroadcastReceiver`在这个两个方法中注册和解除注册
3. foreground lifetime:`onResume` --> `onPause()`

#### 异常情况的生命周期
##### 资源相关的系统配置发生改变导致Activity被杀死并重新创建
activity会调用下面的方法来保存当前的数据,activity在重新创建的时候会恢复数据.

现场保护：`protected void onSaveInstanceState(Bundle outState)`

当系统配置发生改变后，如从横屏手机切换到了竖屏，Activity会被销毁，其onPause,onStop,onDestroy方法均会被调用，同时由于是在异常情况下被终止的，系统会调用onSavedInstanceState来保存当前Activity的状态(正常情况下不会调用此方法)，这个方法的调用时机是在onStop之前，当Activity重新被创建后，系统调用会调用:

    @Override
    protected void onRestoreInstanceState(Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);
    }

##### 资源内存不足导致低优先级的 Activity 被杀死


### Configuration Changes

`Resources.Configuration`类

## 笔记

### activity 跳转方式
- `startActivity()`
- `startActivityForResult()`

意图的写法：

1. 使用目的界面对应的Class

	Intent intent = new Intent(MainActivity.this, NewActivity.class);

2. `setClass(1,2)`方法，参数是报名和类名(或者Class)
3. 使用目的界面类的全限定名，`setClassName()`

	intent.setClassName(this, "com.doublefang.activity");
4. 通过组件名的方式

	ComponnentName cn = new ComponentName(this, NewActivity.class);
	intent.setComponent(cn);

### Activity之间传值
1. intent直接携带数据

	intent.putExtra("name", userName);
2. 将数据存到`Bundle`再通过Intent传递

	intent.putExtras(bundle); ---> Bundle bundle = intent.getExtras()
3. 使用全局类传值
4. `startActivityForResult()`

### activity 启动模式

1. standard:标准模式，默认模式，这种模式启动的activity都在应用的默认栈中
2. singleTop:如果启动的活动正好在栈顶，就不不会创建新的activity，不过会调用`onNewIntent()`方法
3. singleTask:如果栈中已经有需要调用的活动实例，会从新使用该对象，`onNewIntent()`，并且栈中在这个activity上面的活动都会被出栈销毁
4. singleInstance:在新的栈中创建活动实例，例子：多个应用程序调用同一个活动，节省内存，它能保证请求的activity在当前栈中只存在一个；这个栈中只有一个Activity


#### 使用场景

- standard:适合多个实例存在的情况，比如，发邮件页面。
- singleTop:适合接收通知内容显示页面。例如，某些应用会为用户推送一些消息通知，当用户从任务栏中进入查看消息内容界面时，如果设置为singleTop时，这样每次行为都使用同一个实例，用户点击返回时不会存在多个消息页面的情况。
- singleTask: 适合使用在一个程序的主界面。

#### 修改LanchMode方法：

1. 在清单文件的activity标签中添加属性

	android:launchMode="singleTop"
2. 使用`Intent`的`flag`设置启动模式



