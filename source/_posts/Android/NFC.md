---
title: android nfc 开发
date: 2016-04-03 13:18:12
tags: [nfc]
category: android

---

# Android NFC 开发

## 前言
Near Field Communication (NFC) 又称近距离无线通信，是一种短距离的高频无线通信技术，允许电子设备之间进行非接触式点对点数据传输，在十厘米（3.9英寸）内交换数据。

## NFC 的三种工作模式
- 卡模拟模式（Card emulation mode）：将设备模拟成采用RFID技术的IC卡。进行卡模拟的设备必须自带安全组件（Security Element, SE），NFC芯片。
- 点对点模式（P2P mode）：和红外线，蓝牙类似用于数据交换。在Android Beam之后android系统支持了点对点数据传输。
- 读卡器模式（Reader/Writer mode）：作为非接触读卡器使用，比如从海报或者展览信息电子标签上读取相关信息。


## Android 系统检测NFC流程
1. Android设备在开启NFC功能后无论屏幕是否打开都会一直监听设备附近的 NFC tag;
2. 检测到 NFC tag 后会解析内部附带的数据：MIME/URI (不同类型的NFC tag 所携带的数据也不一样);
3. 将解析到的数据存到`intent`中并通过`intent`开启应用;

### intent 类型
下面的排序按优先级从高到低。

1. `ACTION_NDEF_DISCOVERED`:当tag中附带NDEF数据的时候
2. `ACTION_TECH_DISCOVERED`:当tag中没有检测到NDEF数据，但是其中包含的数据是已知的类型
3. `ACTION_TAG_DISCOVERED`:如果上面两种都没有检测到，就使用这个启动应用

![tag 检测流程](https://developer.android.com/images/nfc_tag_dispatch.png)



## NFC 基本使用

### 申请权限
`AndroidManifest.xml`

	<uses-permission android:name="android.permission.NFC" />

### 申明app具有nfc功能 (非必须)
下面标签作用：在应用商店显示本app需要手机支持NFC功能，如果
NFC功能是非必须的，可以不声明，在代码中通过`getDefaultAdapter()`的返回值判断手机是否支持NFC。

	<uses-feature android:name="android.hardware.nfc" android:required="true" />

### Filter 设置
例子1：MIME：`text/plain`

	<intent-filter>
		<action android:name="android.nfc.action.NDEF_DISCOVERED"/>
	        <category android:name="android.intent.category.DEFAULT"/>
		<data android:mimeType="text/plain" />
	</intent-filter>

例子2：URI:`http://developer.android.com/index.html.`


	<intent-filter>
		<action android:name="android.nfc.action.NDEF_DISCOVERED"/>
	        <category android:name="android.intent.category.DEFAULT"/>
		<data android:scheme="http"
	                 android:host="developer.android.com"
		         android:pathPrefix="/index.html" />
	</intent-filter>

#### ACTION_TECH_DISCOVERED
如果定义了这个`action`，必须创建一个XML资源文件定义activity所支持的技术

`/res/xml`文件夹下创建资源文件：(例子)


	<resources xmlns:xliff="urn:oasis:names:tc:xliff:document:1.2">
		<tech-list>
	            <tech>android.nfc.tech.IsoDep</tech>
	            <tech>android.nfc.tech.NfcA</tech>
	            <tech>android.nfc.tech.NfcB</tech>
	            <tech>android.nfc.tech.NfcF</tech>
	            <tech>android.nfc.tech.NfcV</tech>
	            <tech>android.nfc.tech.Ndef</tech>
	            <tech>android.nfc.tech.NdefFormatable</tech>
	            <tech>android.nfc.tech.MifareClassic</tech>
	            <tech>android.nfc.tech.MifareUltralight</tech>
	        </tech-list>
	</resources>

在`AndroidManifest.xml`中创建`<meta-data>`

	<activity>
	...
	<intent-filter>
	    <action android:name="android.nfc.action.TECH_DISCOVERED"/>
	</intent-filter>

	<meta-data android:name="android.nfc.action.TECH_DISCOVERED"
        android:resource="@xml/nfc_tech_filter" />
	...
	</activity>

#### ACTION_TAG_DISCOVERED

	<intent-filter>
	    <action android:name="android.nfc.action.TAG_DISCOVERED"/>
	</intent-filter>

	
### 从`Intent`中获取数据
`intent`中可能携带的数据有下面三种，具体哪个里面有数据取决于检测到的NFC tag中的数据类型

- `EXTRA_TAG`:必须
- `ECTRA_NDEF_MESSAGES`:可选
- `EXTRA_ID`:可选


获取数据的例子：

	public void onResume() { 
	    super.onResume(); 
	    ... 
	    if (NfcAdapter.ACTION_NDEF_DISCOVERED.equals(getIntent().getAction())) {
        	Parcelable[] rawMsgs = intent.getParcelableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES);
	        if (rawMsgs != null) {
        	    msgs = new NdefMessage[rawMsgs.length];
	            for (int i = 0; i < rawMsgs.length; i++) {
	                msgs[i] = (NdefMessage) rawMsgs[i];
        	    } 
	        } 
	    } 
	    //process the msgs array 
	} 

或者：可以从`Intent`中获取`Tag` 对象，tag对象中包含了数据和数据类型

	Tag tag = intent.getParcelableExtra(NfcAdapter.EXTRA_TAG);

### 处理 Tag 对象
获取tag对应的技术标准对象：

技术标准有：

- IsoDep
- MifareClassic
- MifareUltralight
- Ndef
- NdefFormatable
- NfcA
- NfcB
- NfcBarcode
- NfcF
- NfcV

例子：

	IsoDep isoDep = IsoDep.get(tag);

获取到该对象后进行I/O流读写，注意读写代码属于耗时操作需要在子线程中执行

要从nfc卡中读取信息，需要发起一个指令才能收到相应的信息，不同的nfc卡的指令不同。

## 一个完整的Ｄｅｍｏ

[https://github.com/fangmd/NFC_Demo](https://github.com/fangmd/NFC_Demo)



参考:

- [https://zh.wikipedia.org/wiki/%E8%BF%91%E5%A0%B4%E9%80%9A%E8%A8%8A](https://zh.wikipedia.org/wiki/%E8%BF%91%E5%A0%B4%E9%80%9A%E8%A8%8A)
- [https://developer.android.com/guide/topics/connectivity/nfc/nfc.html](https://developer.android.com/guide/topics/connectivity/nfc/nfc.html)
- [http://www.jianshu.com/p/a3a3b3db6b37](http://www.jianshu.com/p/a3a3b3db6b37)
- [http://blog.csdn.net/menghnhhuan/article/details/16947361](http://blog.csdn.net/menghnhhuan/article/details/16947361)
- [http://blog.csdn.net/menghnhhuan/article/details/16947361](http://blog.csdn.net/menghnhhuan/article/details/16947361)
