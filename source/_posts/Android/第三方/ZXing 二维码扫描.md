---
title: ZXing二维码扫描
date: 2016-05-01 22:38:14
tags: ZXing
categories: android

---

# ZXing 二维码扫描

ZXing开源项目地址：[https://github.com/zxing/zxing](https://github.com/zxing/zxing)

## 将ZXing集成到工程中

### 集成方法一
Demo地址：[https://github.com/fangmd/ZXingDemo](https://github.com/fangmd/ZXingDemo)

0. 下载上面的Demo
1. 添加 core.jar 或 'com.google.zxing:core:3.2.1'依赖
2. 将com.google.zxing 整个包（包含路径）复制到自己的项目下。
3. 复制res下的资源文件（×注意×：不要直接复制文件，避免原工程的文件内容被覆盖；可以复制文件中的内容到相应的文件下，如果原工程中没有该文件，可以直接复制文件）
	1. raw下的beep.ogg。音频文件CaptureActivity中使用，若不想用则可以自行修改CaptureActivity
	2. value 下的attrs文件
	3. value下的ids.xml文件 
	4. value下的string内容
	5. value下的color内容
	4. layout下的Activity_scanner 复制到你的layout下
	5. Activity 注册

 		<activity android:name="com.google.zxing.activity.CaptureActivity" />
	6. 权限添加

		<uses-permission android:name="android.permission.VIBRATE" />  <!-- 震动权限 -->
		<uses-permission android:name="android.permission.CAMERA" />
		<uses-feature android:name="android.hardware.camera" />        <!-- 使用照相机权限 -->
		<uses-feature android:name="android.hardware.camera.autofocus" />   <!-- 自动聚焦权限 -->

<!--more-->


## 基本使用

### 生成二维码

	Bitmap mBitmap = EncodingHandler.createQRCode("www.baidu.com", 300);

将“www.baidu.com”生成为二维码，`300`表示二维码的宽高；

### 扫描二维码
使用`Intent`开启扫描Activity，

	Intent intent = new Intent(MainActivity.this, CaptureActivity.class);
	startActivityForResult(intent, REQUEST_CODE);

重写方法`onActivityResult`，获得结果：

	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (resultCode == RESULT_OK) { //RESULT_OK = -1
		    Bundle bundle = data.getExtras();
		    String scanResult = bundle.getString("result");
		    Toast.makeText(MainActivity.this, scanResult, Toast.LENGTH_LONG).show();
		}
	}


## 源码介绍

### CaptureActivity
ZXing暴露的调用Activity。在handleDecode方法中对扫码成功后的动作作处理。

1. `public void handleDecode(Result result, Bitmap barcode)`方法，扫描结束后调用，参数为：扫描的结果和扫描的二维码bitmap

在这个方法中将结果使用`Intent`回传给调用扫描的activity：

		Intent resultIntent = new Intent();
		Bundle bundle = new Bundle();
		bundle.putString("result", resultString);
		bundle.putParcelable("bitmap", barcode);
		resultIntent.putExtras(bundle);
		this.setResult(RESULT_OK, resultIntent);

### CameraManager
1. `public Rect getFramingRect()`方法,定义了扫描的区域

	代码中修改扫描区域大小，使用了屏幕宽度的70%作为扫描区，可自行修改。修改扫描区域大小

### ViewfinderView
ZXing扫码窗口的绘制，原始的ZXing使用这种方式去绘制，在上面提供的开源库中，作者将扫描框的绘制直接抽取到了XML文件中，这样修改起来更加方便了。

1. `private void drawTextInfo(Canvas canvas, Rect frame)`
修改文本绘制的位置

2. `private void drawLaserScanner(Canvas canvas, Rect frame)`
修改扫描线的样式。注意若使用paint.setShader(Shader shader) 方法，一定要在绘制完成后调用paint.setShader(null)。以免绘制信息出错。


### CameraConfigurationManager
修改横竖屏、处理变形效果的核心类。

1. `public void setDesiredCameraParameters(Camera camera, boolean safeMode)`方法中（读取配置设置相机的对焦模式、闪光灯模式等等），可以将扫描改为竖屏：在最后加入：

	/** 设置相机预览为竖屏 */
	camera.setDisplayOrientation(90);

2. `public void initFromCameraParameters(Camera camera)`方法中（计算了屏幕分辨率和当前最适合的相机像素），我们可以对修改为竖屏扫码后，由于像素信息点没有对调造成图像扭曲变形进行修改。

在Log.d(TAG, "Screen resolution: " + screenResolution);后加上如下的代码：

	/** 因为换成了竖屏显示，所以不替换屏幕宽高得出的预览图是变形的 */
	Point screenResolutionForCamera = new Point();
	screenResolutionForCamera.x = screenResolution.x;
	screenResolutionForCamera.y = screenResolution.y;
	// preview size is always something like 480*320, other 320*480
	if (screenResolution.x < screenResolution.y) {
	    screenResolutionForCamera.x = screenResolution.y;
	    screenResolutionForCamera.y = screenResolution.x;
	}	

最后，将screenResolution替换为screenResolutionForCamera：

	cameraResolution = findBestPreviewSizeValue(parameters, screenResolutionForCamera);


### DecodeHandler.decode
ZXing解码的核心类

### CaptureActivityHandler
当DecodeHandler.decode完成解码后，系统会向CaptureActivityHandler发消息。如果编码成功则调用CaptureActivity.handleDecode方法对扫描到的结果进行分类处理。


## 遇到的问题

1. 不能使用Intent传递大于40kb的`bitmap`，可以使用一个单例对象存储和获得`bitmap`



参考/感谢：
- [http://my.oschina.net/smuswc/blog/609413](http://my.oschina.net/smuswc/blog/609413)
- [http://www.jianshu.com/p/4fe2fcfe3389](http://www.jianshu.com/p/4fe2fcfe3389)
- [http://blog.csdn.net/xiaanming/article/details/14450809](http://blog.csdn.net/xiaanming/article/details/14450809)


