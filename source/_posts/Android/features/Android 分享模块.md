---
title: Android 分享模块
date: 2016-04-24 11:38:14
tags: 分享
categories: android

---

# Android 分享模块


## 系统自带的分享功能


>通过系统自带的来启动分享功能
	
	/** * 通过系统的组件进行分享 */ 
	private void shareBySystem(){ 
		Intent intent = new Intent(Intent.ACTION_SEND);
		intent.setType("image/*"); 
		intent.putExtra(Intent.EXTRA_SUBJECT,"Share"); //设置分享的内容 
		intent.putExtra(Intent.EXTRA_TEXT, "I have successfully)"); 
		intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
		startActivity(Intent.createChooser(intent, getTitle())); 
	}

<!--more-->

如果要分享到指定的应用需要反编译app获取包名：

	//com.xxx.xx 为 PackageName
	//com.xxx.xx.activity.ShareActivity 为 ClassName
	intent.setClassName("com.xxx.xx", "com.xxx.xx.activity.ShareActivity");


自己的例子：

    public static void shareBySystem(Activity activity, Share share) {
        Intent intent = new Intent(Intent.ACTION_SEND);
        intent.setType("*/*");
        // 设置分享内容
        intent.putExtra(Intent.EXTRA_TEXT, share.getTitle()+"\r\n"+share.getUrl()); // 分享内容+网页链接(新浪微博可以解析)
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        activity.startActivity(Intent.createChooser(intent, activity.getTitle()));
    }

## ShrareSDK

## 各个应用官方的SDK分享


参考：[http://www.imooc.com/article/1596](http://www.imooc.com/article/1596)
