---
title: Android适配--屏幕适配--基本概念
date: 2016-06-01 09:13:12
tags: [android适配，屏幕适配]
category: Android

---

# 作用

让 app 在不同屏幕尺寸和不同屏幕分辨率的手机上的显示效果相同

# 几个重要的概念

## 屏幕尺寸

- 含义：手机对角线的物理尺寸
- 单位：英寸（inch），1 英寸 = 2.54cm

## 屏幕分辨率

- 含义：手机在横向，纵向上的像素点数总和
- 宽 × 高 例如：1080\*1920，在各个方向上的像素点个数
- 单位：px（pixel），1px = 1 像素点

手机常见的分辨率：320*480,480*800,720*1280,1080*1920

## 屏幕像素密度

- 含义：每英寸的像素点数
- 单位：dpi（dots per ich）

android 手机对于每一类手机屏幕都有相应的屏幕像素密度：

- 低密度(Idpi) , 204\*320, 120dpi
- 中密度(mdpi), 320\*480, 160dpi
- 高密度(hdpi), 480\*800, 240dpi
- 超高密度(xhdpi), 720\*1280, 320dpi
- 超超高密度(xxhdpi), 1080\*1920, 480dpi

nexus6：560dpi

56dp

## 屏幕尺寸，分辨率，像素密度三者之间的关系

![http://upload-images.jianshu.io/upload_images/944365-2b5dc928ab334440.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240](http://upload-images.jianshu.io/upload_images/944365-2b5dc928ab334440.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 密度无关像素

- 含义：device-independent pixel，叫 dp 或者 dip，与终端上的实际物理像素点无关
- 单位：dp，可以保证在不同屏幕像素密度的设备上显示相同的效果

## 独立比例像素

- 含义：scale-independent pixel,叫 sp，sip
- 单位：sp

  > 推荐使用 12sp,14sp,18sp,22sp 作为字体大小值，不推荐使用奇数和小数容易造成精度丢失问题，小于 12sp 的字体太小看不清。

参考：

- [http://www.jianshu.com/p/ec5a1a30694b](http://www.jianshu.com/p/ec5a1a30694b)
- [http://www.jianshu.com/p/ad563d169871](http://www.jianshu.com/p/ad563d169871)
