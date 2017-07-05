---
title: Intent
date: 2016-05-07 11:38:14
tags: Intent
categories: android

---

# Andtoid Intent

## 是什么

Android各个组件之间交互的一种重要方式。

## 作用：

1. 传递数据
2. 启动其他组件

<!--more-->

## 七大属性

1. ComponentName：组件名，指定组件名就是显示Intent
2. Action：标识符，代表一个Intent
3. Category：intent的种类，
4. Data：保存需要传递的数据，值是一个Uri对象，格式为：scheme://host:post/path
5. Type:用于指定Data所指定的Uri对应的MIME类型，image/*,支持通配符
6. Extra：保存需要传递的额外数据，在多个activity之间进行数据交换
7. Flags:标志：定制下一个组件的启动模式



### Intent的使用
1. 显示意图:启动一个指定的组件
2. 隐示意图


### 系统内置的Extra常量
- EXTRA_BCC:存放邮件密送人地址的字符串数组
- EXTRA_CC:存放邮件抄送人地址的字符串数组
- EXTRA_EMAIL:存放邮件地址的字符串数组
- EXTRA_SUBJECT:存放邮件主题字符串数组
- EXTRA_TEXT：存放邮件内容
- EXTRA_KEY_EVENT：以keyevent对象方式存放触发Intent的按键
- EXTRA_PHONE_NUMBER：存放调用ACTION_CALL时的电话号码

### 常用的Data值
1. tell://	号码数据格式，后面跟电话号码
2. mailto://	邮件数据格式，后面跟邮件收件人地址
3. smsto://	短消息数据格式，后面跟短信接收号码
4. content://	内容数据格式，后面跟需要读取的内容
5. file://	文件数据格式，后面跟文件路径
6. market://search?q=pname:pkgname	市场数据格式，在Google Market 里搜索包名为pkgname的应用
7. geo://latitude,longitude	经纬度数据，在地图上显示经纬度所指定的位置



### Action的使用 调用手机上的其他功能

1. Intent.ACTION_MAIN

标识Activity为一个程序的开始。比较常用。

Input:nothing

Output:nothing 

		<activity android:name=".Main" android:label="@string/app_name">   
		<intent-filter>
			 <action android:name="android.intent.action.MAIN" />
			 <category android:name="android.intent.category.LAUNCHER" />
		     </intent-filter>
		</activity> 

2. Intent.Action_CALL

呼叫指定的电话号码。

Input:电话号码。数据格式为：tel:+phone number 

Output:Nothing 

		Intent intent=new Intent(); 
		intent.setAction(Intent.ACTION_CALL);   
		intent.setData(Uri.parse("tel:1320010001");
		startActivity(intent);



3. Intent.Action.DIAL

调用拨号面板

Input:电话号码。数据格式为：tel:+phone number 

Output:Nothing

说明：打开Android的拨号UI。如果没有设置数据，则打开一个空的UI，如果设置数据，action.DIAL则通过调用getData()获取电话号码。

		Intent intent=new Intent();
		intent.setAction(Intent.ACTION_DIAL);   //android.intent.action.DIAL
		intent.setData(Uri.parse("tel:1320010001");
		startActivity(intent); 

4. Intent.Action.ALL_APPS

列出所有的应用。

Input：Nothing.

Output:Nothing.

5. Intent.ACTION_ANSWER

处理呼入的电话。

Input:Nothing.

Output:Nothing.

6. Intent.ACTION_ATTACH_DATA

用于指定一些数据应该附属于一些其他的地方，例如，图片数据应该附属于联系人

Input: Data

Output:nothing

7. Intent.ACTION_BUG_REPORT

显示Dug报告。

Input:nothing

output:nothing

8. Intent.Action_CALL_BUTTON

相当于用户按下“拨号”键。经测试显示的是“通话记录”

Input:nothing

Output:nothing

		Intent intent = new Intent(Intent.ACTION_CALL_BUTTON);
		startActivity(intent);

9. Intent.ACTION_CHOOSER

显示一个activity选择器，允许用户在进程之前选择他们想要的,与之对应的是Intent.ACTION_GET_CONTENT.

10. Intent.ACTION_GET_CONTENT

允许用户选择特殊种类的数据，并返回（特殊种类的数据：照一张相片或录一段音） 

Input: Type

Output:URI

		int requestCode = 1001;
		Intent intent = new Intent(Intent.ACTION_GET_CONTENT); // "android.intent.action.GET_CONTENT"
		intent.setType("image/*"); // 查看类型，如果是其他类型，比如视频则替换成 video/*，或 */*
		Intent wrapperIntent = Intent.createChooser(intent, null);
		startActivityForResult(wrapperIntent, requestCode);  

11. Intent.ACTION_VIEW

用于显示用户的数据。

比较通用，会根据用户的数据类型打开相应的Activity。

比如 tel:13400010001打开拨号程序，http://www.g.cn则会打开浏览器等。

		Uri uri = Uri.parse("http://www.google.com"); //浏览器 
		Uri uri =Uri.parse("tel:1232333"); //拨号程序 
		Uri uri=Uri.parse("geo:39.899533,116.036476"); //打开地图定位 
		Intent it = new Intent(Intent.ACTION_VIEW,uri); 
		startActivity(it); 

		//播放视频 
		Intent intent = new Intent(Intent.ACTION_VIEW); 
		Uri uri = Uri.parse("file:///sdcard/media.mp4"); 
		intent.setDataAndType(uri, "video/*"); 
		startActivity(intent);

		//调用发送短信的程序 
		Intent it = new Intent(Intent.ACTION_VIEW);
		it.putExtra("sms_body", "信息内容..."); 
		it.setType("vnd.android-dir/mms-sms"); 
		startActivity(it);

12. Intent.ACTION_SENDTO 

说明：发送短信息

		//发送短信息 
		Uri uri = Uri.parse("smsto:13200100001"); 
		Intent it = new Intent(Intent.ACTION_SENDTO, uri); 
		it.putExtra("sms_body", "信息内容..."); 
		startActivity(it); 

		//发送彩信,设备会提示选择合适的程序发送 
		Uri uri = Uri.parse("content://media/external/images/media/23"); 
		//设备中的资源（图像或其他资源） 
		Intent intent = new Intent(Intent.ACTION_SEND); 
		intent.putExtra("sms_body", "内容"); 
		intent.putExtra(Intent.EXTRA_STREAM, uri); 
		intent.setType("image/png"); 
		startActivity(it);

		//Email 
		Intent intent=new Intent(Intent.ACTION_SEND); 
		String[] tos={"android1@163.com"}; 
		String[] ccs={"you@yahoo.com"}; 
		intent.putExtra(Intent.EXTRA_EMAIL, tos); 
		intent.putExtra(Intent.EXTRA_CC, ccs);
		intent.putExtra(Intent.EXTRA_TEXT, "The email body text"); 
		intent.putExtra(Intent.EXTRA_SUBJECT, "The email subject text"); 
		intent.setType("message/rfc822"); 
		startActivity(Intent.createChooser(intent, "Choose Email Client"));

13. Intent.ACTION_GET_CONTENT

		//选择图片 requestCode 返回的标识
		Intent intent = new Intent(Intent.ACTION_GET_CONTENT); //"android.intent.action.GET_CONTENT"
		intent.setType(contentType); //查看类型 String IMAGE_UNSPECIFIED = "image/*";
		Intent wrapperIntent = Intent.createChooser(intent, null);
		((Activity) context).startActivityForResult(wrapperIntent, requestCode);  

		//添加音频
		Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
		intent.setType(contentType); //String VIDEO_UNSPECIFIED = "video/*";
		Intent wrapperIntent = Intent.createChooser(intent, null);
		((Activity) context).startActivityForResult(wrapperIntent, requestCode);  

		//拍摄视频 
		int durationLimit = getVideoCaptureDurationLimit(); //SystemProperties.getInt("ro.media.enc.lprof.duration", 60);
		Intent intent = new Intent(MediaStore.ACTION_VIDEO_CAPTURE);
		intent.putExtra(MediaStore.EXTRA_VIDEO_QUALITY, 0);
		intent.putExtra(MediaStore.EXTRA_SIZE_LIMIT, sizeLimit);
		intent.putExtra(MediaStore.EXTRA_DURATION_LIMIT, durationLimit);
		startActivityForResult(intent, REQUEST_CODE_TAKE_VIDEO);

		//视频
		Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
		intent.setType(contentType); //String VIDEO_UNSPECIFIED = "video/*";
		Intent wrapperIntent = Intent.createChooser(intent, null);
		((Activity) context).startActivityForResult(wrapperIntent, requestCode);  

		//录音
		Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
		intent.setType(ContentType.AUDIO_AMR); //String AUDIO_AMR = "audio/amr";
		intent.setClassName("com.android.soundrecorder",
		"com.android.soundrecorder.SoundRecorder");
		((Activity) context).startActivityForResult(intent, requestCode);  

		//拍照 REQUEST_CODE_TAKE_PICTURE 为返回的标识
		Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE); //"android.media.action.IMAGE_CAPTURE";
		intent.putExtra(MediaStore.EXTRA_OUTPUT, Mms.ScrapSpace.CONTENT_URI); // output,Uri.parse("content://mms/scrapSpace");
		startActivityForResult(intent, REQUEST_CODE_TAKE_PICTURE);  



参考：[http://www.cnblogs.com/hanyonglu/archive/2012/03/26/2417278.html](http://www.cnblogs.com/hanyonglu/archive/2012/03/26/2417278.html)
