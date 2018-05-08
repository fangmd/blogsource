---
title: FileProvider
date: 2016-12-13 22:38:14
tags: [Android7.0, FileProvider]
categories: Android

---

# FileProvider

FileProvider 继承自 ContentProvider ,它会创建一个格式为 `content://` 的 Uri 而不是 `file:///`。

内容的 URI 允许我们读写它所指向的文件。当我们要将一个包含 URI 的 Intent 传递到另一个 APP 的时候，可以通过设置 Intent.setFlags() 来添加权限，这个权限的有效期是接收方 Activity 或者 Service 的生命周期。

## Defining a FileProvider

不需要继承 FileProvider 直接在 AndroidManifest 中注册就可以：

	<manifest>
	    ...
	    <application>
	        ...
	        <provider
	            android:name="android.support.v4.content.FileProvider"
	            android:authorities="com.mydomain.fileprovider"
	            android:exported="false"
	            android:grantUriPermissions="true">
	            ...
	        </provider>
	        ...
	    </application>
	</manifest>

如果要做从写里面的方法，也可以继承他，然后注册。

<!--more-->
## Specifying Available Files

FileProvider 只会生成指向预先指定的目录的 content URI.

定义目录：

	<paths xmlns:android="http://schemas.android.com/apk/res/android">
	    <files-path name="my_images" path="images/"/>
	    ...
	</paths>

`paths` 包含一个以上的下列子标签：

- `file-path`:可以通过 `Context.getFileDir()` 获取地址
- `cache-path`:可以通过 `Context.getCacheDir()` 获取地址
- `external-path`:可以通过 `Environment.getExternalStorageDirectory()` 获取地址
- `external-files-path`:可以通过 `Context.getExternalFilesDir(String)` 获取地址
- `external-cache-path`:可以通过 `Context.getExternalCacheDir()` 获取地址

把 `path` 标签写入到 xml 文件，比如`res/xml/file_paths.xml`,在 AndroidManifest 中定义 `meta-data`：

	<provider
	    android:name="android.support.v4.content.FileProvider"
	    android:authorities="com.mydomain.fileprovider"
	    android:exported="false"
	    android:grantUriPermissions="true">
	    <meta-data
	        android:name="android.support.FILE_PROVIDER_PATHS"
	        android:resource="@xml/file_paths" />
	</provider>

## Generating the Content URI for a File

	File imagePath = new File(Context.getFilesDir(), "images");
	File newFile = new File(imagePath, "default_image.jpg");
	Uri contentUri = FileProvider.getUriForFile(getContext(), "com.mydomain.fileprovider", newFile);
	//content://com.mydomain.fileprovider/my_images/default_image.jpg.

# 调用系统相机方法

## 添加 FileProvider

AndroidManifest：

	<provider
		android:name="android.support.v4.content.FileProvider"
		android:authorities="com.waytone.normal"
		android:grantUriPermissions="true"
		android:exported="false">
		<meta-data
			android:name="android.support.FILE_PROVIDER_PATHS"
			android:resource="@xml/file_paths" />
	</provider>

res/xml/file_paths.xml:

	<?xml version="1.0" encoding="utf-8"?>
	<resources>
	    <paths>
	        <external-path path="" name="camera_photos" />
	    </paths>
	</resources>

Activity:

	@Override
	public void getPhotoFromSysCamera(long photoName) {
		Intent openCameraIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
		String photoFilePath = FileUtils.getPhotoFilePath(photoName);
		File file = new File(photoFilePath);
		if (!file.getParentFile().exists()) file.getParentFile().mkdirs();
		Uri imageUri = FileProvider.getUriForFile(this, BuildConfig.APPLICATION_ID, file);//通过FileProvider创建一个content类型的Uri
		openCameraIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); //添加这一句表示对目标应用临时授权该Uri所代表的文件
		Logger.d("图片文件存储在：" + photoFilePath);
		openCameraIntent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri);
		startActivityForResult(openCameraIntent, REQUEST_CODE_TAKE_PHOTO_FROM_CAMERA);
	}





参考：

- [官方API](https://developer.android.com/reference/android/support/v4/content/FileProvider.html)
- [https://inthecheesefactory.com/blog/how-to-share-access-to-file-with-fileprovider-on-android-nougat/en](https://inthecheesefactory.com/blog/how-to-share-access-to-file-with-fileprovider-on-android-nougat/en)
