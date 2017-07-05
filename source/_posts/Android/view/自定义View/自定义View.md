---
title: 自定义View
date: 2016-03-28 09:23:14
tags: [View, 自定义View]
categories: android

---


## 继承特定的View(比如 TextView)



## 继承特定的ViewGroup(比如 LinearLayout)

### 组合View
通过layout布局文件创建组合自定义View

    public class SearchHistoryText extends FrameLayout {

        private TextView mText;
        private View mClose;

        public SearchHistoryText(Context context) {
            this(context, null);
        }

        public SearchHistoryText(Context context, AttributeSet attrs) {
            this(context, attrs, 0);
        }

        public SearchHistoryText(Context context, AttributeSet attrs, int defStyleAttr) {
            super(context, attrs, defStyleAttr);
            initView(context);
        }

        private void initView(Context context) {
            LayoutInflater.from(context).inflate(R.layout.search_history_text, this);
            mText = (TextView) findViewById(R.id.search_history_text);
            mClose = findViewById(R.id.search_history_text_close);
        }



## 继承View

### 构造方法
>有四个构造方法，第四个版本要求高一般不用

构造方法一：

    public CustomView(Context context) {
        this(context, null);
    }

<!--more-->

构造方法二：2和3是在xml中创建view的时候使用的

    public CustomView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

构造方法三：在构造方法三中初始化画笔，构造方法互相调用(参数少的调用参数多的)简化代码。

    public CustomView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        mPaint = new Paint();
    }

#### 在xml文件中创建view：

java中使用类的时候不需要导包的情况：1. 同包下 2. lang包下

xml也一样，它没有同包的类，系统会默认导入2个包：1. android.view包 2. android.widget

自定义view导入方式： 1. 写全包名+类名 2. class属性(第二种好像不行了)

    <com.doublefang.customview.CustomView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#f00"/>

### 绘制方法
在view的`onLayout()`方法后执行

	protected void onDraw(Canvas canvas)

绘制条件：画笔-- paint，画布 canvas

由两种情况不需要画笔：drawARGB ， drawColor
#### 绘制规则图案
一般先清空画布：

	canvas.drawColor(Color.WHITE);

画笔的一些设置：

	mPaint.setColor(Color.BLACK); // 画笔的颜色
	mPaint.setStrokeWidth(10); // 画笔的粗细：10*10的正方形 点的坐标：中心点
	mPaint.setStyle(Paint.Style.STROKE); // 设置画笔style：1. Paint.Style.STROKE：只画边框    2. Paint.Style.Fill 画实心

画布执行绘制方法：

	canvas.drawPoint(100, 100, mPaint); // 画点：默认单位长度为像素，可改变 ; 绘制出来的点取决于画笔的属性：颜色，粗细 ； 这里的点是正方形的 ；
	canvas.drawLine(200, 200, 400, 400, mPaint); // 画线：1. 起点和终点+画笔 2. 点数组+画笔 3. 点数组+ int offset, int count+画笔
	canvas.drawRect(200, 500, 600, 700, mPaint);  // 画矩形： 1. 二个点的xy(float)+画笔 2. 矩形对象+画笔 3. 矩形对象F(以float存四个点的信息)+画笔 ; 矩形对象作用：里面封装了一些方法比如判断点是否在矩形内
	canvas.drawCircle(500, 500, 300, mPaint); // 画圆： 1. 圆心+半径
	canvas.drawOval(new RectF(200, 500, 600, 700), mPaint); // 画椭圆： 1. 版本21矩形内切：参数就是矩形左上，右下xy坐标+画笔 2. 版本低，使用矩形对象
	canvas.drawArc(new RectF(200, 500, 600, 700), 0, 60, true, mPaint); // 扇形 1. 版本23  参数3：false表示画弧线 2.  版本低，使用矩形对象 ； 角度：0：水平右 顺时针方向
	 // 弧线 画笔设置成填充风格的时候：两端点连线形成闭合图形

#### 绘制多边形：使用Path对象

	Path path = new Path();
	path.moveTo(100, 100); // 移动不画
	path.lineTo(200, 200);  // 移动画
	// path.arcTo();
	canvas.drawPath(path, mPaint);


**注意：**基线的值，只有在画笔设置了textSize的时候才会生成

	// 画出基线
	mPaint.setStrokeWidth(5);
	mPaint.setColor(Color.BLUE);
	canvas.drawLine(100, 300 + top, 800, 300 + top, mPaint);
	mPaint.setColor(Color.RED);
	canvas.drawLine(100, 300 + ascent, 800, 300 + ascent, mPaint);
	mPaint.setColor(Color.YELLOW);
	canvas.drawLine(100, 300 + leading, 800, 300 + leading, mPaint);
	mPaint.setColor(Color.RED);
	canvas.drawLine(100, 300 + descent, 800, 300 + descent, mPaint);
	mPaint.setColor(Color.GREEN);
	canvas.drawLine(100, 300 + bottom, 800, 300 + bottom, mPaint);

画个五角星：

    private void drawStar(Canvas canvas) {
        canvas.drawColor(Color.WHITE);
        int cx = 500, cy = 500, radius = 300;
        Path path = new Path();
        path.moveTo(cx, cy - radius);
        mPaint.setStyle(Paint.Style.STROKE);
        for (int i = 0; i <= 5; i++) {
            path.lineTo(cx + (float) Math.sin(Math.toRadians(i * 144)) * radius, cy - (float) (Math.cos(Math.toRadians(i * 144)) * radius));
        }
        canvas.drawPath(path, mPaint);
    }

#### 绘制图片
>不能在`onDraw()`方法里面加载图片，加载图片属于耗操作

>图片也可以作为画笔的着色器使用

可以在构造方法中加载图片：

    public CustomView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        mPaint = new Paint();
        mBitmap = BitmapFactory.decodeResource(context.getResources(), R.mipmap.a);
    }

画布直接通过画笔绘制图片：3个方案

	canvas.drawBitmap(mBitmap, 100, 100, mPaint);
	canvas.drawBitmap(mBitmap
                , new Rect(0, 0, mBitmap.getWidth(), mBitmap.getHeight()) // 图片的大小，可以用作从原图中截取
                , new Rect(0, 0, getWidth(), getHeight() / 2)  // 图片放置区域
                , mPaint);  // 图片会铺满参数3区域，会被拉伸;

方案三:矩阵

	Matrix matrix = new Matrix(); // 12个方法， 3*4
	//matrix.setTranslate(100,100); // set 清空当前矩阵，后执行相应方法
	matrix.preTranslate(100, 100); // pre 正序执行，在单前状态下插入一个动作
	matrix.preRotate(45, 0, 0);
	//matrix.postTranslate(100,100); // post 后序执行， 在单前状态后插入动作
	matrix.setScale(2, 0.5f); // 宽度放大一倍，高度一半； 后面还可以跟两个参数设置锚点
	matrix.setSkew(1, 0); // 倾斜
        // 矩阵的设定有前后顺序

	canvas.drawBitmap(mBitmap, matrix, mPaint);

镜像翻转

	matrix.setScale(1, -1, mBitmap.getWidth() / 2, mBitmap.getHeight() / 2); // -x 表示水平镜像，-y垂直镜像

抗锯齿 比较耗时，

	mPaint.setAntiAlias(true); // 抗锯齿 比较耗时

#### 画笔设置
渐变：

1. 线性渐变：两种

		// 1. 线的两个坐标+两个颜色;
		LinearGradient linearGradient = new LinearGradient(100, 100, getWidth(), 100, Color.RED, Color.BLUE, Shader.TileMode.CLAMP);

		// 线性渐变：2. 多彩变化
        LinearGradient linearGradient2 = new LinearGradient(100, 100, getWidth(), 400
                , new int[]{0xffff0000, 0xffff8800, 0xffffff00, 0xff00ff00, 0xff00ffff, 0xff0000ff, 0xffff00ff}
                , new float[]{0, 1.0f / 6, 2.0f / 6, 3.0f / 6, 4.0f / 6, 5.0f / 6, 1}  // 颜色的位置 0-1之间的值
                , Shader.TileMode.CLAMP);

		// 参数7：瓦片模式
        // 1. Shader.TileMode.CLAMP 超出的范围，以最近的颜色点作为自己的颜色 2. Shader.TileMode.MIRROR 镜像r->b b->r 3. Shader.TileMode.REPEAT: r->b r-b 会有明显的分割线

2. 扩散渐变：两种

		RadialGradient radialGradient = new RadialGradient(300, 200, 300, Color.RED, Color.BLUE, Shader.TileMode.CLAMP);

3. 扇形渐变：两种

		SweepGradient sweepGradient = new SweepGradient(getWidth() / 2, getHeight() / 2
                , new int[]{0xffff0000, 0xffff8800, 0xffffff00, 0xff00ff00, 0xff00ffff, 0xff0000ff, 0xffff00ff}
                , new float[]{0, 1.0f / 6, 2.0f / 6, 3.0f / 6, 4.0f / 6, 5.0f / 6, 1});

**设置着色器**

>作用：常用是将图片做成圆形的头像


图片shader对象 头像展示 --- 两个瓦片模式：1. 水平超出范围 2. 垂直超出范围

	BitmapShader bitmapShader = new BitmapShader(mBitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);  // 图片只能对应到0,0点

	mPaint.setShader(bitmapShader); // 设置渐变风格

绘制圆：

	canvas.drawCircle(300, 300, 300, mPaint);

定制图片的位置：
>图片不能移动，所以只能移动画布

	canvas.save();
	canvas.translate(500,500);
	canvas.drawCircle(300,300,300,mPaint);//300,300是相对画布的位置，画布移动的时候坐标原点也改变了，所以不用改变xy
	canvas.restore();

画布保存和恢复：成对使用，先写避免忘记

	canvas.save();
	canvas.save(Canvas.ALL_SAVE_FLAG); //  保存什么内容
	canvas.restore(); // 不会更改前面绘制图案
	canvas.restoreToCount(3); // 恢复前几次


### 触摸方法
	public boolean onTouchEvent(MotionEvent event)

获取动作：

	int action = event.getAction(); // 单点触摸的时候使用
	event.getActionMasked(); // 多点操作

四种动作：

	MotionEvent.ACTION_DOWN
	MotionEvent.ACTION_MOVE
	MotionEvent.ACTION_UP
	MotionEvent.ACTION_CANCEL
	// 多点和单点触控一样，增加动作：
	MotionEvent.ACTION_POINTER_UP // 表示第二个手指抬起
	MotionEvent.ACTION_POINTER_DOWN // 表示第二个手指按下

获取屏幕上点的个数：

	event.getPointerCount();

点的坐标,当前view的坐标系下：

	event.getX(); //单点
	event.getX(0); // 多点
	event.getY();
	event.getX(0);

屏幕上的绝对坐标：

	event.getRawX();
	event.getRawY();

获取手指在屏幕上的大小：

	event.getSize();
	event.getSize(0);


### 自定义属性
1. 创建相应的setter方法

		// 自定义属性
	    public void setPaintColoc(int color){
	        mPaint.setColor(color);
	    }

	    public void setPaintWidth(int width){
	        mPaint.setStrokeWidth(width);
	    }

2. xml创建资源文件：`atts.xml`

		<?xml version="1.0" encoding="utf-8"?>
		<resources>
		    	<declare-styleable name="TouceView">
				<attr name="paintColor" format="color"/>
				<attr name="paintWidth" format="dimension"/>
		    	</declare-styleable>
		</resources>

3. 构造方法中获取设置的自定义属性

	    public TouceView(Context context, AttributeSet attrs, int defStyleAttr) {
	        super(context, attrs, defStyleAttr);
	        mPaint = new Paint();
	        mPath = new Path();
	        mPaint.setStyle(Paint.Style.STROKE);
	        // 如果是new出来的对象 attrs为null
	        if (attrs != null) {
	            TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.TouceView);
	            mPaint.setColor(typedArray.getColor(R.styleable.TouceView_paintColor, Color.RED));
	            float dimension = typedArray.getDimension(R.styleable.TouceView_paintWidth, 20);
	            mPaint.setStrokeWidth(dimension);
	        } else {
	            mPaint.setColor(Color.BLACK);
	            mPaint.setStrokeWidth(5);
	            mPaint.setStyle(Paint.Style.STROKE);
	        }
	    }

4. 布局文件中的设置

		xmlns:app="http://schemas.android.com/apk/res-auto"

	可改名也可不该，可导入一部分也可全导

		xmlns:touch="http://schemas.android.com/apk/TouchView"

	控件

		<com.doublefang.customview3.TouceView
        	android:id="@+id/main_canvas"
        	android:layout_width="match_parent"
        	android:layout_height="match_parent"
        	touch:paintColor="#00f"
        	touch:paintWidth="10dp"
        />

## 继承ViewGroup

>自定义布局控件


###　添加触控效果

	public class TouchLayout extends FrameLayout {
	    private int postition = -1;
	    private float mLastY;

	    public TouchLayout(Context context) {
	        super(context);
	    }

	    public TouchLayout(Context context, AttributeSet attrs) {
	        super(context, attrs);
	    }

	    public TouchLayout(Context context, AttributeSet attrs, int defStyleAttr) {
	        super(context, attrs, defStyleAttr);
	    }

	    @Override
	    public boolean onTouchEvent(MotionEvent event) {

	        if (getChildCount() > 0) {
	            if (postition == -1) {
	                postition = getChildCount() - 1;
	                // 让后面的图片以方法的方式出现
	                for (int i = 0; i < postition; i++) {
	                    View child = getChildAt(i);
	                    ViewCompat.setScaleX(child, 0);
	                    ViewCompat.setScaleY(child, 0);
	                }
	            }
	            switch (event.getAction()) {
	                case MotionEvent.ACTION_DOWN:
	                    mLastY = event.getY();
	                    break;
	                case MotionEvent.ACTION_MOVE:
	                    if (mLastY > event.getY()) {
	                        if (postition < getChildCount() - 1) {
	                            View child = getChildAt(postition + 1);
	                            ViewCompat.setTranslationY(child, ViewCompat.getTranslationY(child) + event.getY() - mLastY);
	                            float offset = ViewCompat.getTranslationY(child) / child.getHeight();
	                            ViewCompat.setScaleX(getChildAt(postition), offset);
	                            ViewCompat.setScaleY(getChildAt(postition), offset);
	                        }

	                    } else {
	                        if (postition > 0) {
	                            View child = getChildAt(postition);
	                            ViewCompat.setTranslationY(child, ViewCompat.getTranslationY(child) + event.getY() - mLastY);
	                            float offset = ViewCompat.getTranslationY(child) / child.getHeight();
	                            ViewCompat.setScaleX(getChildAt(postition - 1), offset);
	                            ViewCompat.setScaleY(getChildAt(postition - 1), offset);
	                        }
	                    }
	                    mLastY = event.getY();
	                    break;
	                case MotionEvent.ACTION_UP:
	                case MotionEvent.ACTION_CANCEL:
	                    View child = getChildAt(postition);
	                    if (postition > 0) {
	                        if (ViewCompat.getTranslationY(child) > child.getHeight() / 3) {
	                            ViewCompat.animate(child).translationY(child.getHeight()).start();
	                            ViewCompat.animate(getChildAt(postition - 1)).scaleY(1).scaleX(1).start();
	                            postition--;
	                        } else {
	                            ViewCompat.animate(child).translationY(0).start();
	                            ViewCompat.animate(getChildAt(postition - 1)).scaleY(0).scaleX(0).start();
	                        }
	                    }

	                    if (postition < getChildCount() - 1) {
	                        if (ViewCompat.getScaleX(child) > 0.3f) {
	                            ViewCompat.animate(child).scaleX(1).scaleY(1).start();
	                            ViewCompat.animate(getChildAt(postition + 1)).translationY(getChildAt(postition + 1).getHeight()).start();
	                        } else {
	                            ViewCompat.animate(child).scaleX(0).scaleY(0).start();
	                            ViewCompat.animate(getChildAt(postition + 1)).translationY(0).start();
	                            postition++;
	                        }
	                    }
	                    break;
	            }
	        }
	        return true;
	    }
