---
title: Broadcast
date: 2016-03-24 21:38:14
tags: Broadcast
categories: android

---


# Broadcast

**涉及到的API**

- `BroadcastReceiver`
- `IntentFilter`
- `LocalBroadcastManager`
- `ContextCompat`
- `ActivityCompat`

## 广播接收者 BroadcastReceiver

创建一个`BroadcastReceiver`的继承类

在`onReceive()`方法里面对获取的广播进行处理。

在`onReceive`里面开启一个activity的方法：

	Intent intent = new Intent(context, MainActivity.class);
	intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TOP);
	context.startActivity(intent);

拦截广播：`abortBroadcast();` 对有序广播才有效
原因：Receiver 没有任务栈，启动activity的时候要新建一个任务栈，这里改变了活动的启动方式


## 广播注册

### 静态注册：在xml清单文件这个注册

>广播不会强制要求注册，不报错

快捷创建

	<receiver
		android:name=".HeadestBroacastReceiver"
		android:enabled="true"
		android:exported="true">
		<intent-filter>
			<action android:name="android.intent.action.HEADSET_PLUG"/>
		</intent-filter>
	</receiver>

#### 注册文件中的一些设置
- 优先级：`android:priority="10"`在`intent-fliter`过滤器的属性
- 常用监听项：
	- `android.intent.action.BATTERY_CHANGED`
	- `android.intent.action.NEW_OUTGOING_CALL`
	- `android.intent.action.PHONE_STATE`
	- `android.provider.Telephony.SMS_RECEIVED`
	- `android.net.conn.CONNECTIVITY_CHANGE`
	- `android.intent.action.HEADSET_PLUG`
	- `android.intent.action.SCREEN_OF`
	- `android.intent.action.SCREEN_ON`
	- `android.intent.action.BOOT_COMPLETED`

**注意：**有些监听需要加权限，对于敏感权限需要在code里面确定获取。
#### 获取权限方法
**注意：**权限获取的代码执行完后，在执行需要权限的代码的时候还是要判断一下是否获取了权限。

	    // 获取敏感权限
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_PHONE_STATE) == PackageManager.PERMISSION_DENIED) {
            ActivityCompat.requestPermissions(this,new String[]{Manifest.permission.READ_PHONE_STATE},0);
        }


相应的结果方法：

	@Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
            Toast.makeText(MainActivity.this, "权限获取成功", Toast.LENGTH_SHORT).show();
        }else{
            Toast.makeText(MainActivity.this, "权限获取失败", Toast.LENGTH_SHORT).show();
            finish();
        }
    }
获取失败可以有两种操作方法：1. 结束activity 2. 继续获取权限 3. 如果这个权限不是必要的就可以不操作


### 动态注册
>动态注册方式需要注册代码执行后，app才能获取接收广播的功能。

>动态注册的优先级和静态注册一样的时候，动态注册会先收到广播

注册代码：

	OtherReceiver receiver = new OtherReceiver();
	IntentFilter filter = new IntentFilter();
	fliter.setPriority(30);
	fliter.addAction(....);
	registerReceiver(receiver, fliter);

## 有序广播与无序广播的发送
都需要一个Intent，intent参数为：action的值比如`com.example.broadcasttest.MY_BROADCAST`

有序广播：

	sendOrderedBroadcast(intent,null);
参数2：一个与权限相关的字符串

无序广播：

	sendBroadcast(intent);

## 本地广播
>本地广播发送的广播，只能被在本地广播注册的`receiver`接收

>通过本地广播管理器注册的接收器只能接收本地广播发出的广播。

- 获取本地广播管理器

		LocalBroadcastManager manager= LocalBroadcastManager.getInstance(this);


- 通过本地广播管理器注册`receiver`

		manager.registerReceiver(mOReceiver, fliter);
- 通过本地广播管理器发送广播

		Intent intent = new Intent(.action..)；manager.sendBroadcast(intent);
- 解除注册

		manager.unregisterReceiver(localReceiver);

注意：接收者可以同时在本地和全局注册,这样就能同时接收本地广播和系统广播。

**为什么本地广播不能被静态注册的广播接收者接收：**

	因为本地广播只有在程序运行的时候才有可能会发出广播，而静态注册的广播在程序不运行的时候就能发挥作用，所以，本地广播不需要使用静态注册的功能。


