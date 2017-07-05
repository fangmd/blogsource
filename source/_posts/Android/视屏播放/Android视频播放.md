---
title: Android 视频播放
date: 2016-05-10 14:18:12
tags: 视频播放
category: android

---

# 视频播放 

## VideoView MediaController

### 获取对象

	mVideoView = (VideoView) findViewById(R.id.main_videoView);

### 设置媒体路径
本地

	mVideoView.setVideoPath(new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), "陈奕迅 - 陪你度过漫长岁月.mp4").getAbsolutePath());

网络

	mVideoView.setVideoURI(Uri.parse("...."));

### 开始

	mVideoView.start();

### 加入控制台

	MediaController mediaController = new MediaController(this);
	mVideoView.setMediaController(mediaController);


## MediaPlayer SurfaceView
见[SurfaceView]()


