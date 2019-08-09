---
title: Service
date: 2016-03-24 21:38:14
tags: Service
categories: android

---

# New

1. Service 不是一个单独的进程 ，它和应用程序在同一个进程中。
2. Service 不是一个线程，所以我们应该避免在Service里面进行耗时的操作。
3. IntentService 具有异步功能，有子线程


**涉及API**

- `Service`
- `IntentService`
- `IBinder`
- `Binder`
- `ServiceConnection`
- `Handler`
- `Messenger`
- `Message`
- `AIDL`

# Service简介

Service 是一个可以在后台执行长时间操作而不使用用户界面的应用组件。其他的应用组件可以启动 Service，并且即使用户切换了界面Service也会在后台运行。此外，组件可以绑定服务与之交互，甚至是执行进程间通信(IPC)。

**注意：**Service运行在主进程中的主线程中，`IntentService`类有运行在自己的线程中不会再主线程中运行。

## 创建Service
所有的Service必须在`AndroidManifest.xml`中注册

>android:name：属性是唯一必需的属性，用于指定服务的类名。
android:enabled：是否可被系统实例化，默认true.
android:exported : 设为false则其它应用(user ID不同)无法启动此服务。默认true。
android:permission：指定启动服务及其运行所在进程所需的权限。
android:process：指定服务运行进程，默认当前应用进程。

注意：Android5.0以后禁止了隐式声明Intent来启动Service。可以通过setPackage()解决或者把一个隐式Intent转换成显式Intent

## Service开启方式
1. `Context.startService()`
2. `Context.bindService()`

### Service生命周期

![](http://i.imgur.com/pzuXMgy.png)

- 两种启动方式
- 两种模式：`onStartCommand()`的返回值**一般使用系统默认的返回值就可以了**
	- `START_STICKY`：当系统由于内存不足杀死Service后，Service会尝试重新启动；用户杀死Service的时候不会重新启动。
	- `START_NOT_STICKY`和`START_REDELIVER_INTENT`

**注意：**如果activity执行unbindService()时，会解除接口和指定服务的绑定，解除绑定会调用服务的onUnbind()方法，如果服务没有任何绑定的的内容，并且没有执行过startService，那么服务就会销毁。


## 权限
Service可以被其他应用启动如果它是公开的。

要使用其他应用的Service需要添加权限`<uses-permission>`

### 进程的生命周期
- 如果一个Service正在运行其内部的方法：`onCreate()`，`onStartCommand()`，`onDestroy()`，进程会确保其执行完成。
- Service只有在内存过低的时候才会被系统销毁。 如果一个服务已经被执行完，它的优先级就会比屏幕上可见的线程低，但是比其他不可见的线程高。 有时候：长期在后台驻留的服务可能会被杀死。
- 如果Service和其他东西绑定，服务所在的进程的优先级就会变高。绑定对Service优先级的影响可以通过标志确定：`BIND_ABOVE_CLIENT`，`BIND_ALLOW_OOM_MANAGEMENT`，`BIND_WAIVE_PRIORITY`，`BIND_IMPORTANT`，`BIND_ADJUST_WITH_ACTIVITY`
- 一个开启后的Service可以使用`startForeground(int, Notification)`方法让服务处在前台的状态，让系统以为他属于用户正在的activity，从而不会再内存不足的时候杀死他。

## 绑定Service的停止时机

1. 当你的`activity`需要在可见时才需要同`service`交互则需要在activity的`onStart()`中绑定service，并在`onStop()`方法中解除绑定。

2. 若当在activity在后台时仍需要与service交互，则需要在`onCreate()`方法中绑定，并在`onDestory()`方法中解除绑定。

# Service同时通过start和bind方式开启的情况

![](https://img.readitlater.com/i/upload-images.jianshu.io/upload_images/1662720-88ca4b4a6030961e/QS/imageMogr2%252Fauto-orient%252Fstrip%25257CimageView2%252F2%252Fw%252F1240/EQS/RS/w704.png)

>可以看到当所有与service绑定的组件都调用了unBind()方法时，若在这之后service调用stopSelf()或其他组件调用stopService()方法后service才会被销毁。这期间有别的组件重新调用onBind()方法，若该service的onUnbind()方法返回true则会调用onRebind()方法，若返回false则会直接调用onBind()方法。


# Activity操作Service

>关键：在Activity中获取到启动的Service实例。

`Activity`绑定启动`Service`

		public boolean bindService(Intent service, ServiceConnection conn, int flags)
里面`ServiceConnection`是一个接口，有两个抽象方法：

		public void onServiceConnected(ComponentName name, IBinder service)；
		public void onServiceDisconnected(ComponentName name)；
通过抽象方法1的参数2：IBinder获取Service实例，该参数是Service的`onBinder()`的返回值，返回一个BInder对象，所以需要在Service中写一个内部类，将`MusicService`对象传给Activity，然后`getService()`获得Service实例：

		public class MusicBinder extends Binder {
			public MusicService getService() {
				return MusicService.this;
			}
		}

		@Override
		public IBinder onBind(Intent intent) {
			return new MusicBinder();
		}


# Service操作Activity
>关键：Handler的消息机制

1. 在activity中创建一个`Handler`对象
2. 通过`Handler`对象new出一个`Messenger`
3. 将`Messenger`通过intent传给`Service`

		Messenger message = new Messenger(mHandler);
        intent.putExtra("msg", message); // 可传的对象：需要实现Parcelable接口 作为序列化 handler没有实现序列化

        // 混合启动模式
        startService(intent); // 先state 再绑定 为了让解除绑定的时候不让Service销毁
        bindService(intent, this, BIND_AUTO_CREATE);
4. `Service`端接收`intent`获取`Messenger`
5. 使用`Messenger`送`Message`对象给activity

		message.what = MainActivity.UPDATE_PLAY_BUTTON;
		message.obj = mPlayer.isPlaying();
		mMessenger.send(message);

5. 在`Activity`端处理传过来的`Message`


# IntentService

它在 `Service` 的基础上增加了异步任务的功能，其中`onHandleIntent()`方法是抽象方法必须重写，方法内的代码是在子线程中执行的，用作耗时操作。

>IntentService只适合执行一个耗时任务，不适合长期操作。

通常我们在使用这个类的时候只需要写**构造方法**和**onHandleIntent()**方法；

- 在耗时操作完成的时候，会自动调用`stopSelft()`结束服务


>它创建了一个独立的工作线程来处理所有的通过onStartCommand()传递给服务的intents。

>创建了一个工作队列，来逐个发送intent给onHandleIntent()。

>默认实现的onBind()返回null

>默认实现的onStartCommand()的目的是将intent插入到工作队列中

# 其他

## bindService 如何实现生命周期绑定

[https://juejin.im/post/5c13cd99e51d4522ad6e74ec](https://juejin.im/post/5c13cd99e51d4522ad6e74ec)

1. bindService 方法执行时，LoadedApk 会记录 ServiceConnection 信息
2. Activity 执行 finish 方法时，会通过 LoadedApk 检查 Activity 是否存在未注销/解绑的 BroadcastReceiver 和 ServiceConnection，如果有，那么会通知 AMS 注销/解绑对应的 BroadcastReceiver 和 Service，并打印异常信息，告诉用户应该主动执行注销/解绑的操作


