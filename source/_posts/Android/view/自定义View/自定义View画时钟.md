---
title: 自定义View画时钟
date: 2016-03-28 20:38:14
tags: view
categories: android

---
需要注意的：

1. 屏幕适配
2. 加背景的时候要先移动画布
3. 如果要让秒针匀速运动，更新频率需要很高
4. 画指针： 1. 偏移量， 2. 旋转画布

<!--more-->

	public class ClockView extends View implements Handler.Callback {
	
	    private Paint mPaint;
	
	    private Handler mHandler = new Handler(this);
	    private Bitmap mBitmap;
	    private BitmapShader mBitmapShader;
	
	    public ClockView(Context context) {
	        this(context, null);
	    }
	
	    public ClockView(Context context, AttributeSet attrs) {
	        this(context, attrs, 0);
	    }
	
	    public ClockView(Context context, AttributeSet attrs, int defStyleAttr) {
	        super(context, attrs, defStyleAttr);
	        mPaint = new Paint();
	        mHandler.sendEmptyMessage(0);
	
	        // 设置背景 新建一个paint专门画背景
	        mBitmap = BitmapFactory.decodeResource(context.getResources(), R.mipmap.a);
	        mBitmapShader = new BitmapShader(mBitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
	    }
	
	    @Override
	    protected void onDraw(Canvas canvas) {
	        super.onDraw(canvas);
	
	        // 先写save 和 restore
	        canvas.save();
	        canvas.drawColor(Color.WHITE);
	
	        // 如何让时钟适配屏幕大小
	        // 居中 :先平移 再缩放 ??? 或者直接改xml大小位置
	        float min = Math.min(getWidth() / 1000.0f, getHeight() / 1000.0f);
	        canvas.translate(getWidth() / 2, getHeight() / 2);
	        canvas.scale(min, min); //在操作前做缩放
	
	        // 加背景
	        canvas.save();
	        Paint tempPaint = new Paint();
	        tempPaint.setShader(mBitmapShader);
	        canvas.translate(-500, -500);
	        canvas.drawCircle(500, 500, 500, tempPaint);
	        canvas.restore();
	
	        mPaint.setStrokeWidth(3);
	        mPaint.setColor(Color.BLUE);
	        mPaint.setStyle(Paint.Style.STROKE);
	        canvas.drawCircle(0, 0, 500, mPaint);
	
	        canvas.save();
	        for (int i = 0; i < 12; i++) {
	            if (i % 3 == 0) {
	                mPaint.setStrokeWidth(9);
	            } else {
	                mPaint.setStrokeWidth(3);
	            }
	            canvas.drawLine(0, 450, 0, 500, mPaint);
	            canvas.rotate(30, 0, 0);
	        }
	        canvas.restore();
	
	        // 画一个圆心
	        Paint centerPaint = new Paint();
	        centerPaint.setColor(Color.WHITE);
	        canvas.drawCircle(0, 0, 10, centerPaint);
	
	        Calendar calendar = Calendar.getInstance();
	        canvas.save();
	        mPaint.setStrokeWidth(3);
	        mPaint.setColor(Color.RED);
	        canvas.rotate(calendar.get(Calendar.SECOND) * 6 + calendar.get(Calendar.MILLISECOND) / 1000.0f * 6, 0, 0);
	        canvas.drawLine(0, 50, 0, -430, mPaint); // 画秒针：初始位置为0
	        canvas.restore();
	
	        // 分针
	        canvas.save();
	        mPaint.setStrokeWidth(6);
	        mPaint.setColor(Color.BLACK);
	        canvas.rotate(calendar.get(Calendar.MINUTE) * 6 + calendar.get(Calendar.SECOND) / 60.0f * 6, 0, 0);
	        Path miPath = new Path();
	        miPath.moveTo(0, -400);
	        miPath.lineTo(20, -200);
	        miPath.lineTo(0, 0);
	        miPath.lineTo(-20, -200);
	        miPath.lineTo(0, -400);
	        canvas.drawPath(miPath, mPaint);
	        canvas.restore();
	
	        // 时针
	        canvas.save();
	        canvas.rotate(calendar.get(Calendar.HOUR) * 30 + calendar.get(Calendar.MINUTE) / 60f * 30, 0, 0);
	        Path hPath = new Path();
	        hPath.moveTo(0, -300);
	        hPath.lineTo(30, -150);
	        hPath.lineTo(0, 0);
	        hPath.lineTo(-30, -150);
	        hPath.lineTo(0, -300);
	        canvas.drawPath(hPath, mPaint);
	        canvas.restore();
	
	        canvas.restore();
	
	
	    }
	
	    @Override
	    public boolean handleMessage(Message msg) {
	        switch (msg.what) {
	            case 0:
	                // 通知刷新
	                invalidate(); // 同步刷新：优点快，缺点：只能在主线程中使用
	                postInvalidate(); // 异步刷新：优点是能在不同线程中使用，缺点：慢
	                mHandler.sendEmptyMessageDelayed(0, 30); // 如果要让秒针匀速运动，更新频率需要很高
	                break;
	        }
	        return true;
	    }
	}

xml

    <com.doublefang.customview.ClockView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>