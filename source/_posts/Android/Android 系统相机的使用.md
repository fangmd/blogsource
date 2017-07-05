---
title: Android 系统相机和相册调用的使用
date: 2016-04-24 11:38:14
tags: 相机
categories: android

---

#  Android 系统相机和相册调用的使用

## 系统现有相机应用的调用
>在开发的应用中调用系统现有应用，需要使用Intent指定开启的应用的Action和Category，然后通过startActivity(Intent)或者startActivityForResult(Intent,int)开启指定的Activity，如果使用startActivityForResult()方法开启并需要返回值，再重写onActivityResult(int,int,Intent)即可。

     <activity
	            android:name="com.android.camera.Camera"
	            android:clearTaskOnLaunch="true"
	            android:configChanges="orientation|keyboardHidden"
	            android:screenOrientation="landscape"
	            android:taskAffinity="android.task.camera"
	            android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen" >
	            <intent-filter>
	                <action android:name="android.intent.action.MAIN" />
	                <categroy android:name="android.intent.category.DEFAULT" />
	                <categroy android:name="android.intent.category.LAUNCHER" />
	            </intent-filter>
	            <intent-filter>
	                <action android:name="android.media.action.IMAGE_CAPTURE" />
	                <categroy android:name="android.intent.category.DEFAULT" />
	            </intent-filter>
	            <intent-filter>
	                <action android:name="android.media.action.STILL_IMAGE_CAMERA" />
	                <categroy android:name="android.intent.category.DEFAULT" />
	            </intent-filter>
	        </activity>
	        <activity
	            android:name="com.android.camera.VideoCamera"
	            android:clearTaskOnLaunch="true"
	            android:configChanges="origientation|keyboardHidden"
	            android:label="@string/video_camera_label"
	            android:screenOrientation="landscape"
	            android:taskAffinity="android.task.camcorder"
	            android:theme="@android:style/theme.Black.NoTitleBar.Fullscreen" >
	            <intent-filter>
	                <action android:name="android.media.action.VIDEO_CAMERA" />
	                <categroy android:name="android.intent.category.DEFAULT" />
	            </intent-filter>
	            <intent-filter>
	                <action android:name="android.media.action.VIDEO_CAPTURE" />
	                <categroy android:name="android.intent.category.DEFAULT" />
	            </intent-filter>
	        </activity>


- android.media.action.IMAGE_CAPTURE：Intent的Action类型，从现有的相机应用中请求一张图片。
- android.media.action.VIDEO_CAPTURE：Intent的Action类型，从现有的相机应用中请求一段视频。

上面两个参数，均在MediaStore类中以静态常量的形式定义好了，分别是：MediaStore.ACTION_IMAGE_CAPTURE(相机)和MediaStore.ACTION_VIDEO_CAPTURE(摄像机)。

<!--more-->

## 系统现有相机拍摄照片
>在使用系统现有相机用用的时候，默认会把图片保存到系统图库的目录下，如果需要指定图片文件的保存路径，需要额外在Intent中设置。

	Intent intent=new Intent();
	// 指定开启系统相机的Action
    intent.setAction(MediaStore.ACTION_IMAGE_CAPTURE);
    intent.addCategory(Intent.CATEGORY_DEFAULT);
    // 根据文件地址创建文件
    File file=new File(FILE_PATH);
    // 把文件地址转换成Uri格式
    Uri uri=Uri.fromFile(file);
    // 设置系统相机拍摄照片完成后图片文件的存放地址
    intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);

设置文件的方式：

	name = DateFormat.format("yyyyMMdd_hhmmss",  
	                Calendar.getInstance(Locale.CHINA))  
	                + ".jpg";  
	Uri imageUri = Uri.fromFile(new File(PATH, name));  

## 获取系统现有相机拍摄的图片
>在新开启的Activity中，如果需要获取它的返回值，则需要使用startActivityForResult(Intent,int)方法开启Activity，并重写onActivityResult(int,int,Intent)获取系统相机的返回数据，那么我们只需要在onActivityResult()中获取到返回值即可。

>系统相机拍摄的照片，如果不指定路径，会保存在系统默认文件夹下，可以使用Intent.getExtra()方法得到，得到的是一个Uri地址，表示了一个内容提供者的地址。如果通过MediaStore.EXTRA_OUTPUT指定了保存路径，那么通过Intent.getExtra()得到的将是一个空地址，但是既然是我们指定的地址，那么也不愁找不到它了。

### 没有设置指定位置时

	Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	startActivityForResult(intent, REQUEST_CODE_CAMERA);

	@Override
	 protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	   super.onActivityResult(requestCode, resultCode, data);
	   if (requestCode == REQUEST_CODE_CAMERA && resultCode == RESULT_OK) {
	      Bundle bundle = data.getExtras();
	      // 获取相机返回的数据，并转换为Bitmap图片格式 ，这是缩略图
	      Bitmap bitmap = (Bitmap) bundle.get("data");
	   }
	 }

### 指定了照片的保存路径
	
	if (requestCode == REQUEST_CODE_CAMERA && resultCode == RESULT_OK) {
		   mPhoto.setImageURI(data.getData());
	}



##系统现有相机拍摄图片Demo


	package cn.bgxt.callsystemcamera;
	
	import java.io.File;
	
	import android.app.Activity;
	import android.content.Intent;
	import android.net.Uri;
	import android.os.Bundle;
	import android.provider.MediaStore;
	import android.util.Log;
	import android.view.View;
	import android.widget.Button;
	
	public class SysVideoCameraActivity extends Activity {
	    private Button btn_StartVideoCamera;
	    private static final String FILE_PATH = "/sdcard/sysvideocamera.3gp";
	    private static final String TAG="main";
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_sysvideocamera);
	
	        btn_StartVideoCamera = (Button) findViewById(R.id.btn_StartVideoCamera);
	        btn_StartVideoCamera.setOnClickListener(click);
	    }
	
	    private View.OnClickListener click = new View.OnClickListener() {
	
	        @Override
	        public void onClick(View v) {
	            Intent intent = new Intent();
	            intent.setAction("android.media.action.VIDEO_CAPTURE");
	            intent.addCategory("android.intent.category.DEFAULT");
	            File file = new File(FILE_PATH);
	            if(file.exists()){
	                file.delete();
	            }
	            Uri uri = Uri.fromFile(file);
	            intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);
	            startActivityForResult(intent, 0);
	        }
	    };
	    
	
	    @Override
	    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	        Log.i(TAG, "拍摄完成，resultCode="+requestCode);
			 if (resultCode == RESULT_OK) {
				
			}
	    }
	
	}

# 相册调用

	Intent intent = new Intent(Intent.ACTION_GET_CONTENT);  
	intent.setType("image/*");  
	startActivityForResult(intent, CAMERA_SELECT);  
