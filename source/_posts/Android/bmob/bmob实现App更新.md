---
title: Bomb实现App更新
date: 2016-06-04 13:18:12
tags: [Bmob,App更新]
category: android

---

# Bmob实现App更新

## 官方文档
地址:[http://docs.bmob.cn/data/Android/e_autoupdate/doc/index.html#index_快速入门](http://docs.bmob.cn/data/Android/e_autoupdate/doc/index.html#index_快速入门)

### 0 导入SDK
[http://docs.bmob.cn/data/Android/b_developdoc/doc/index.html#index_SDK导入](http://docs.bmob.cn/data/Android/b_developdoc/doc/index.html#index_SDK导入)

#### 方法一：添加依赖仓库
`project/build.gradle`:

	repositories {
	     jcenter()
	     //Bmob的maven仓库地址--必填
	     maven { url "https://raw.github.com/bmob/bmob-android-sdk/master"  }
	        
	}

在`app/build.gradle`中添加需要的依赖：

	dependencies {
	    compile fileTree(dir: 'libs', include: ['*.jar'])

	        //以下SDK开发者请根据需要自行选择
		    //bmob-sdk：Bmob的android sdk包，包含了Bmob的数据存储、文件等服务，以下是最新的bmob-sdk:
		        //3.4.7-aar：请务必查看下面注释[1]
	    compile 'cn.bmob.android:bmob-sdk:3.4.7-aar'

	        //bmob-push：Bmob的推送包
	    compile 'cn.bmob.android:bmob-push:0.8'

	    //bmob-im：Bmob的即时通讯包，注意每个版本的im依赖特定版本的bmob-sdk，具体的依赖关系可查看下面注释[2]
	    compile 'cn.bmob.android:bmob-im:2.0.5@aar'
	    compile 'cn.bmob.android:bmob-sdk:3.4.7-aar'
	    //bmob-sms ：Bmob单独为短信服务提供的包
	    compile 'cn.bmob.android:bmob-sms:1.0.1'

	   //如果你想应用能够兼容Android6.0，请添加此依赖(org.apache.http.legacy.jar)
	   compile 'cn.bmob.android:http-legacy:1.0'

	}

*注意：*这里如果我们使用retrofit的话，`okhttp`的依赖就重复了，如果打包的时候报错，建议通过手动导入jar和so文件的方式来加入SDK

#### 方法二：手动导入SDK jar so
1. 将下载的sdk中的jar文件复制到工程的libs目录下(注意不要复制okhttp相关的jar，如果你的工程中已经添加了这个依赖)
2. 将包含so文件的文件夹复制到`app/src/main/jniLibs`这个文件夹可能需要自己创建

#### AndroidManifest.xml配置

sdk需要的权限：

	<uses-permission android:name="android.permission.INTERNET" /> 
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" /> 
	<uses-permission android:name="android.permission.WAKE_LOCK" /> 
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	<uses-permission android:name="android.permission.READ_PHONE_STATE" />

ps:其中有两个敏感权限

### 1 初始化SDK
在代码中初始化SDK
	 
	// 初始化BmobSDK 
	Bmob.initialize(this, APPID);

### 3 版本设置
*app的版本号和版本码设置在`app/build.gradle`中，如果在`AndroidManifest.xml`中设置，还是会被覆盖；*


### 4 app更新相关api
官方Demo：[https://github.com/bmob/bmob-android-demo-autoupdate/blob/master/src/com/bmob/autoupdatedemo/MainActivity.java](https://github.com/bmob/bmob-android-demo-autoupdate/blob/master/src/com/bmob/autoupdatedemo/MainActivity.java)


# 未解决
2. SDK初始化代码放在`BaseApplication`中失败
