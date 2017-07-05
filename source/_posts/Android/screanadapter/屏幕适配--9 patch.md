---
title: 屏幕适配--9 patch
date: 2016-11-15 09:13:12
tags: [适配, 屏幕适配]
category: Android

---

NinePatch图片以*.9.png结尾，和普通图片的区别是四周多了一个边框

制作 9patch 图的时候需要在图片的四周画黑线：

1. 左边那条黑色线代表图片垂直拉伸的区域
2. 上边的那条黑色线代表水平拉伸区域
3. 右边的黑色线代表内容绘制的垂直区域
4. 下边的黑色线代表内容绘制的水平区域

<!--more-->

**注意：**右边和下边的线是可选的，左边和上边的线不能省略。

## Android studio 制作 9 patch

将需要制作的 `png` 图片复制到 android 工程 `drawable` 目录下，点击其右键：

[Android studio png右键菜单](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/androidstudio9patchmenu.png)

选择 `create 9-patch file`

点击 `OK`

出现 `.9.png` 图片，这个图片就是 9-patch 图，我们需要对其进行编辑（画黑线）

双击 9-patch 图片，进行编辑：

[Android studio 9patch 底菜单](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/androidstudio9patch.png)

**注意:**底菜单中要选中 9-patch 页面

菜单意思：

- Zoom: Adjust the zoom level of the graphic in the drawing area.

    用来缩放左边编辑区域的大小

- Patch scale: Adjust the scale of the images in the preview area.

    用来缩放右边预览区域的大小

- Show lock: Visualize the non-drawable area of the graphic on mouse-over.

    当鼠标在图片区域的时候显示不可编辑区域

- Show patches: Preview the stretchable patches in the drawing area (pink is a stretchable patch), as shown in figure 2, above.

    在编辑区域显示图片拉伸的区域 （使用粉红色来标示）

- Show content: Highlight the content area in the preview images (purple is the area in which content is allowed), as shown in figure 2.

    在预览区域显示图片的内容区域（使用浅紫色来标示）

- Show bad patches: Adds a red border around patch areas that may produce artifacts in the graphic when stretched, as shown in figure 2. Visual coherence of your stretched image will be maintained if you eliminate all bad patches.


参考：

- [http://www.cnblogs.com/slider/archive/2011/12/07/2279302.html](http://www.cnblogs.com/slider/archive/2011/12/07/2279302.html)
- [https://developer.android.com/studio/write/draw9patch.html](https://developer.android.com/studio/write/draw9patch.html)
