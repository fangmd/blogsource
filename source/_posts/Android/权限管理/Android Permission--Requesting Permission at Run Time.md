---
title: Android Permission--Requesting Permission at Run Time
date: 2016-07-09 13:18:12
tags: [permission，官方文档]
category: android

---

# Requesting Permission at Run Time  

官方地址:[https://developer.android.com/training/permissions/requesting.html](https://developer.android.com/training/permissions/requesting.html)

Android 6.0 (API level 23) 开始

1. app在运行的时候才会去获取权限,而不是在app安装的时候. 这个改进简化了app安装的步骤,因为app不需要在安装的时候去获取权限. 
2. 用户可以更多的控制app的功能:因为用户可以在设置中关闭app已经获取到的权限

权限分为:普通权限和危险权限

在所有的Android版本中,上面的两种权限都需要在清单文件中声明(如果权限是属于需要声明的权限),但是不同的Android版本在权限声明后的作用是不用的:

1. Android 6.0一下的版本,权限只要在清单文件中声明了,app正常安装就表示权限获取到了
2. Android 6.0及以上的版本,权限声明后,危险权限还需要在代码中重新进行申请,而且申请后的权限随时会被用户取消,所以在设计app的时候尽量保证没有获取到权限也能正常运行.

<!--more-->

## 检查危险权限
需要在所有需要危险权限的操作前检查危险权限是否获取到了.

检查权限的方法:`ContextCompat.checkSelfPermission()`,该方法返回的参数:`PackageManager.PERMISSION_GRANTED`或者`PackageManager.PERMISSION_DENIED`

## 请求危险权限

### 解释为什么app需要这个权限

1. 确定app需要这个危险权限,并向用户解释为什么需要这个权限
2. ` shouldShowRequestPermissionRationale()`:返回`true`如果用户拒绝了app获取权限

### 获取需要的权限


	// Here, thisActivity is the current activity 
	if (ContextCompat.checkSelfPermission(thisActivity,
		        Manifest.permission.READ_CONTACTS) 
		!= PackageManager.PERMISSION_GRANTED) {
	 
	    // Should we show an explanation? 
	    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
		    Manifest.permission.READ_CONTACTS)) { 
	 
		// Show an expanation to the user *asynchronously* -- don't block 
		// this thread waiting for the user's response! After the user 
		// sees the explanation, try again to request the permission. 
	 
	    } else { 
	 
		// No explanation needed, we can request the permission. 
	 
		ActivityCompat.requestPermissions(thisActivity,
		        new String[]{Manifest.permission.READ_CONTACTS},
		        MY_PERMISSIONS_REQUEST_READ_CONTACTS); 
	 
		// MY_PERMISSIONS_REQUEST_READ_CONTACTS is an 
		// app-defined int constant. The callback method gets the 
		// result of the request. 
	    } 
	} 

### 处理权限请求结果
用处在处理完权限获取的弹窗后会执行下面的方法:


	@Override 
	public void onRequestPermissionsResult(int requestCode,
		String permissions[], int[] grantResults) {
	    switch (requestCode) {
		case MY_PERMISSIONS_REQUEST_READ_CONTACTS: { 
		    // If request is cancelled, the result arrays are empty. 
		    if (grantResults.length > 0
		        && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
	 
		        // permission was granted, yay! Do the 
		        // contacts-related task you need to do. 
	 
		    } else { 
	 
		        // permission denied, boo! Disable the 
		        // functionality that depends on this permission. 
		    } 
		    return; 
		} 
	 
		// other 'case' lines to check for other 
		// permissions this app might request 
	    } 
	} 














