---
title: SurfaceView
date: 2016-04-18 11:38:14
tags: SurfaceView
categories: android

---

# SurfaceView 

## 和自定义view比较
自定义View：

1. 只能在主线程中绘制
2. 绘制效率低
3. 封装性好，便于复用
4. 可以通过自定义属性配置

SurfaceView：

1. 封装性差
2. 可以在子线程中绘制
3. 绘制速度是普通view绘制的5倍，主要用于游戏和视频播放
4. 不可配置

## 一个基本的使用

### run接口实现类

	public class DrawRunnable implements Runnable {
	    private static final String TAG = DrawRunnable.class.getName();
	    private SurfaceHolder mHolder;
	    private boolean running;
	    private final Paint mPaint;
	
	    public DrawRunnable(SurfaceHolder holder) {
	        mHolder = holder;
	        running = true;
	        mPaint = new Paint(); // 创建画笔耗时
	        mPaint.setTextSize(100);
	        mPaint.setColor(Color.BLUE);
	    }
	
	    @Override
	    public void run() {
	        while (running) {
	            Canvas canvas = mHolder.lockCanvas();
	            if (canvas != null) {
	                canvas.drawColor(Color.WHITE);
	                canvas.drawText("Surface绘制", 200, 200, mPaint);
	                mHolder.unlockCanvasAndPost(canvas);
	            }
	        }
	    }
	
	    public void setRunning(boolean runable) {
	        running = runable;
	    }
	}

### mainactivity

    mSurface = (SurfaceView) findViewById(R.id.main_surface);
	SurfaceHolder holder = mSurface.getHolder();
    holder.addCallback(this);
    mDrawRunnable = new DrawRunnable(holder);
    new Thread(mDrawRunnable).start();

### holder.addCallback(this);接口方法

    @Override
    public void surfaceCreated(SurfaceHolder holder) {

    }

    @Override
    public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {

    }

    @Override
    public void surfaceDestroyed(SurfaceHolder holder) {
        mDrawRunnable.setRunning(false); // 结束绘制
    }

## 播放视频

    try {
        mMediaPlayer = new MediaPlayer();
        File file = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), "陈奕迅 - 陪你度过漫长岁月.mp4");
        File externalStorageDirectory = Environment.getExternalStorageDirectory();
        mMediaPlayer.setDataSource(file.getAbsolutePath());
    } catch (Exception e) {
        e.printStackTrace();
    }

    @Override
    public void surfaceCreated(SurfaceHolder holder) {
        mMediaPlayer.setDisplay(holder); // 设定展示平台
        mMediaPlayer.setOnPreparedListener(this);
        mMediaPlayer.prepareAsync();
    }

    @Override
    public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {

    }

    @Override
    public void surfaceDestroyed(SurfaceHolder holder) {
		// 及时关闭
        mMediaPlayer.stop();
        mMediaPlayer.release();
    }

    @Override
    public void onPrepared(MediaPlayer mp) {
		int videoWidth = mMediaPlayer.getVideoWidth();
        int videoHeight = mMediaPlayer.getVideoHeight();
        ViewGroup.LayoutParams layoutParams = mSurface.getLayoutParams();
        layoutParams.height = videoHeight;
        layoutParams.width = videoWidth;
        mSurface.setLayoutParams(layoutParams);
        //mMediaPlayer.setVideoScalingMode(MediaPlayer.VIDEO_SCALING_MODE_SCALE_TO_FIT_WITH_CROPPING);
        mMediaPlayer.start();
    }



