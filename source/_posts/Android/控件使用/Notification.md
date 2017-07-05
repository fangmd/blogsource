---
title: Notification
date: 2016-03-24 21:38:14
tags: Notification
categories: android

---


**涉及API**

- `Notification`
- `NotificationManager`
- `PendingIntent`
- `RemoteView`

# 使用步骤

## 第一步：获取系统服务
	NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
## 第二步：创建`Notification`
### 创建方法一：使用远程View
	Notification noti = new Notification();
	RemoteViews remoteViews = new RemoteViews(getPackageName(), R.layout.通知布局文件);
	noti.contentView = remoteViews;

### 创建方法二：`Notification.Builder`  兼容方法` NotificationCompat.Builder`
	Notification noti = new NotificationCompat.Builder(this)
		.setContentTitle("标题")
		.setContentText("content")
		.setSubText("subText")
		.setSmallIcon(R.mipmap.图片)
		.setLargeIcon(BitmapFactory.decodeResource(getResources(), R.mipmap.图片))
		.setVibrate(new long[]{300,300,300,300})//震动
		.setAutoCancel(true)
		.setLights(0ffff0000,1000,1000)
		.build();

设置通知不能被取消：

	mNotification.flags |= Notification.FLAG_NO_CLEAR;

方式二使用自定义布局：.setContent(remoteView)
### 消息的其他设置
大视图通知：

	if (Build.VERSION.SDK_INT > Build.VERSION_CODES.JELLY_BEAN) {
            mNotification.bigContentView = remoteViews;
	}

flags：

	// 设置flags
	notification.flags |= Notification.FLAG_NO_CLEAR;
	// 取消flags
	notification.flags &= ~Notification.FLAG_NO_CLEAR;
	// 判断是否设置了
	if ((notification.flags & Notification.FLAG_NO_CLEAR) != 0) {

	}

设置属性有两种方式：1. 直接用相应的方法。 2. 通过flags(有些属性只能通过flags)

### PendingIntent使用
	views.setOnClickPendingIntent(R.id.play, PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_UPDATE_CUTTENT))
## 第三步：发送消息
	manager.notify(0,noti); //参数1：标识 参数2:消息对象



# 扩展
## activity的`onNewIntent`方法
activity中的`protected void onNewIntent(Intent intent)`方法是在活动假启动的时候执行的。

当通知调用已经在任务栈顶的活动的时候，在这个方法内执行对通知的修改

    @Override
    protected void onNewIntent(Intent intent) {
        Log.d("Intent", "onNewIntent--------------");
        super.onNewIntent(intent);
        String text = intent.getStringExtra("text");
        Toast.makeText(MainActivity.this, text, Toast.LENGTH_SHORT).show();

        // 修改通知状态
        if (mNotification != null) {
            mNotification.contentView.setImageViewResource(R.id.noti_play, android.R.drawable.ic_media_pause);
            // 重新发送通知，并覆盖原来的通知
            NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
            mNotification.defaults &= ~Notification.DEFAULT_VIBRATE; // 关闭震动
            manager.notify(0, mNotification);
        }
    }


# RemoteView
>提供了一组基础的操作用于跨进程更新它的界面，在android中的使用场景是**通知栏**和**桌面小部件**

作用：自定义通知栏和桌面小部件

## 构造方法
	RemoteViews(String packageName, int layoutId) // getPackageName()

	RemoteViews(RemoteViews landscape, RemoteViews portrait)

	RemoteViews(Parcel parcel)

## 点击事件
需要使用`pendingIntent`

	remoteView.setOnClickPendingIntent(int viewId, PendingIntent pendingIntent)
参数1：远程控件布局中的子控件的`id`，

## 桌面小部件：`AppWidgetProvider`

1定义小部件的界面 在res/layout下创建xml

2定义小部件的配置信息 在res/xml下创建xml文件  四个主要参数

initialLayout 桌面小工具使用的初始化布局

minHeight minWidth  最小宽高

updatePeriodMillis 小部件自动更新周期 单位毫秒

3.定义小部件的实现类  extends AppWidgetProvider

onReceive onUpdate onWidgetUpdate onEnable onDisable onDeleted onReceive;

4.清单文件中声明小部件



# PendingIntent
等待意图

pendingIntent表示在这个意图会在不确定的时候发生其他功能和Intent一样。

# Icon Size LargeIcon
[图标大小](http://iconhandbook.co.uk/reference/chart/android/)

LargeIcon:

- ldpi: 48x48 px *0.75
- mdpi: 64x64 px *1.00
- hdpi: 96x96 px *1.50
- xhdpi: 128x128 px *2.00
- xxhdpi: 192x192 px *3.00

最好的创建 LargeIcon 的方式： 需要 vector

	BitmapDrawable contactPicDrawable = (BitmapDrawable) ContactsUtils.getContactPic(mContext, contactId);
	Bitmap contactPic = contactPicDrawable.getBitmap();

	Resources res = mContext.getResources();
	int height = (int) res.getDimension(android.R.dimen.notification_large_icon_height);
	int width = (int) res.getDimension(android.R.dimen.notification_large_icon_width);
	contactPic = Bitmap.createScaledBitmap(contactPic, width, height, false);


SmallIcon:

24dp


# 设置声音

	Uri alarmSound = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);

	.setSound(alarmSound)

# 设置悬浮

	.setPriority(Notification.PRIORITY_MAX)


# stack notifications

[https://opbeat.com/community/posts/building-stack-notifications-on-android/](https://opbeat.com/community/posts/building-stack-notifications-on-android/)

[https://gist.github.com/waynepiekarski/775dcd9105bd5c376eb4](https://gist.github.com/waynepiekarski/775dcd9105bd5c376eb4)


如果要将 通知合并成group

1. 设置 group id
2. 发送一个 notification 作为父 group，它包含所有子 notification

例子：

	private void notiText() {
		Notification group = new Notification.Builder(getActivity())
				.setSmallIcon(R.mipmap.img_upload_grey_noti_small_ic)
				.setGroupSummary(true)
				.setGroup("GROUP")
				.build();
		Notification group1 = new Notification.Builder(getActivity())
				.setSmallIcon(R.mipmap.img_upload_grey_noti_small_ic)
				.setContentTitle("这是第一条消息")
				.setContentText("你好, 单刀土豆")
				.setGroup("GROUP")
				.build();
		Notification group2 = new Notification.Builder(getActivity())
				.setSmallIcon(R.mipmap.img_upload_grey_noti_small_ic)
				.setContentTitle("这是第二条消息")
				.setContentText("我们又见面了")
				.setGroup("GROUP")
				.build();
		Notification group3 = new Notification.Builder(getActivity())
				.setSmallIcon(R.mipmap.img_upload_grey_noti_small_ic)
				.setContentTitle("这是第三条消息")
				.setContentText("再见")
				.setGroup("GROUP")
				.build();
		NotificationManager notificationManager =
				(NotificationManager) getActivity().getSystemService(NOTIFICATION_SERVICE);

		notificationManager.notify(2, group1);
		notificationManager.notify(3, group2);
		notificationManager.notify(4, group3);
		notificationManager.notify(1, group);
	}

## 使用

	/**
	  * 显示 图片上传 通知
	  *
	  * @param controlFileEvent 图片上传信息类
	  */
	 public static void showImgNoti(ControlFileEvent controlFileEvent) {

		 NotificationManager notificationManager = (NotificationManager) QChatApplication.getInstance().getSystemService(Context.NOTIFICATION_SERVICE);

		 Intent intent = new Intent(QChatApplication.getInstance(), MediaControlActivity.class);
		 intent.putExtra(MediaControlActivity.MEDIA_TYPE, MediaControlActivity.STARTType.TYPE_PHOTO);
		 PendingIntent pendingIntent = PendingIntent.getActivity(QChatApplication.getInstance(), 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);

		 Notification notification = new Notification.Builder(QChatApplication.getInstance())
				 .setSmallIcon(R.mipmap.img_upload_grey_noti_small_ic)
				 .setContentTitle(controlFileEvent.desc)
				 .setContentText(controlFileEvent.uim)
				 .setAutoCancel(true)
				 .setLargeIcon(BitmapFactory.decodeResource(QChatApplication.getInstance().getResources(), R.mipmap.img_upload_blue_noti_large_ic))
				 .setContentIntent(pendingIntent)
				 .setGroup(NOTIFICATION_IMAGE_GROUP)
				 .build();

		 notificationManager.notify(((int) System.currentTimeMillis()), notification);

		 showImgGroupNotiIfNeed2(notification);
	 }

	 /**
	  * Android 7.0 group notification
	  *
	  * @param notification 当前发送的 notification
	  */
	 public static void showImgGroupNotiIfNeed2(Notification notification) {
		 if (Build.VERSION.SDK_INT >= 23) {
			 NotificationManager notificationManager = (NotificationManager) QChatApplication.getInstance().getSystemService(Context.NOTIFICATION_SERVICE);

			 ArrayList<StatusBarNotification> groupedNotifications = new ArrayList<>();

			 // step through all the active StatusBarNotifications and
			 for (StatusBarNotification sbn : notificationManager.getActiveNotifications()) {
				 if (notification.getGroup() != null &&
						 notification.getGroup().equals(sbn.getNotification().getGroup())) {
					 groupedNotifications.add(sbn);
				 }
			 }

			 if (groupedNotifications.size() > 1) {

				 Notification notification2 = new Notification.Builder(QChatApplication.getInstance())
						 .setSmallIcon(R.mipmap.img_upload_grey_noti_small_ic)
						 .setLargeIcon(BitmapFactory.decodeResource(QChatApplication.getInstance().getResources(), R.mipmap.img_upload_blue_noti_large_ic))
						 .setGroup(NOTIFICATION_IMAGE_GROUP)
						 .setGroupSummary(true)
						 .build();

				 notificationManager.notify(((int) System.currentTimeMillis()), notification2);

			 }
		 }

	 }
