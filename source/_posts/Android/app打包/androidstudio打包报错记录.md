---
title: Android Studio 依赖重复解决
date: 2016-04-03 13:18:12
tags: [打包,android studio, 依赖重复]
category: android

---

# Android Studio 依赖重复解决方案

## 重复依赖

	Error:Error converting bytecode to dex:
	Cause: com.android.dex.DexException: Multiple dex files define Lokhttp3/Address;

### 0 方案一 允许重复

	defaultConfig {        
	    // Enabling multidex support.
	    multiDexEnabled true
	}

### 1 方案二 去除依赖

如果在：

	dependencies { 。。。 }

中有明显的重复依赖，直接删除

去除内部依赖：

	compile ('com.squareup.okhttp3:okhttp-urlconnection:3.0.0') {
		exclude group: 'com.squareup.okhttp3' 
	}

### 2 方案二 设置依赖为已提供还

适合导入model的时候使用：

右键module选择open module setting,选择要修改的module名，切换到dependencies页面，将要修改的jar的scope修改provided模式。


参考：
- [http://stackoverflow.com/questions/28917696/errorexecution-failed-for-task-appdexdebug-com-android-ide-common-process](http://stackoverflow.com/questions/28917696/errorexecution-failed-for-task-appdexdebug-com-android-ide-common-process)
- [http://glblong.blog.51cto.com/3058613/1694630](http://glblong.blog.51cto.com/3058613/1694630)
