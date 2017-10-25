---
title: Animation 总结
date: 2016-03-15 21:38:14
tags: Animation
categories: android

---


```
2017/10/25 添加 http://hencoder.com/ui-1-6/ 内容


```

## 动画分类
- 补间动画
- 帧动画
- 属性动画

<!--more-->

## 动画属性
- translate:平移动画
- scale:缩放动画
- rotate:旋转动画
- alpha:透明度
- interpolator
- duration

## 创建动画的方式
- xml
- code

## 帧动画

### 作用
- 设计弥补安卓不能播放gif图

### 创建动画--xml
#### 创建xml动画属性

- 在drawable下创建资源文件
- 切换到`Project`下创建更好
- 修改根结点为:`animation-list`
- 设置item项

根结点的属性:

- `xmlns:android="http://schemas.android.com/apk/res/android"`
- ` android:visible="true"`
- `android:variablePadding="true"`
- `android:oneshot="true"`:是否循环

item属性:

- `android:drawable="@mipmap/sample_flat_45"`:图片资源
- `android:duration="100"`:持续时间

例子：
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <animation-list xmlns:android="http://schemas.android.com/apk/res/android">

        <item
            android:drawable="@drawable/img"
            android:duration="70"/>
        <item
            android:drawable="@drawable/img1"
            android:duration="70"/>
        <item
            android:drawable="@drawable/img2"
            android:duration="70"/>

    </animation-list>
```

#### 使用xml创建动画 `@drawable`

在布局资源文件中,将这个动画资源文件设置到控件的背景属性或者其他可设置`@drawable`的属性中

	第一种情况:设置的是背景,那就需要获取背景并转为`AnimationDrawable`对象,最后调用`AnimationDrawable`的`start()`方法开始动画
	1. `android:background="@drawable/fram"`
	2. `AnimationDrawable ad = (AnimationDrawable)view(对象).getBackground()`
	3. `ad.start();`

	第二种情况:给`ImageView`设置`src`属性,需要获取`getDrawable()`并强转...
	1. `android:src="@drawable/fram"`
	2. `AnimationDrawable ad = ((AnimationDrawable) mImageView.getDrawable());`
	3. `ad.start();`

### 纯代码创建动画

	// 2. code 生成动画
        mAnimationDrawable = new AnimationDrawable();
        mAnimationDrawable.setOneShot(false);
        mAnimationDrawable.addFrame(getResources().getDrawable(R.drawable.ic_account_circle_black_24dp), 100);
        mAnimationDrawable.addFrame(getResources().getDrawable(R.drawable.ic_account_circle_green_24dp), 100);
        mAnimationDrawable.addFrame(getResources().getDrawable(R.drawable.ic_account_circle_blue_24dp), 100);
        mAnimationDrawable.addFrame(getResources().getDrawable(R.drawable.ic_account_circle_red_24dp), 100);
	// mFrameImg2 为ImageView, 注意不能设置src属性
        mFrameImg2.setBackground(mAnimationDrawable);
	// 动画开始,停止
	mAnimationDrawable.start();
	mAnimationDrawable.stop();



## 补间动画

### 特点
- 没有改变控件的属性
- 在动画完毕后控件回到其属性设置的位置和状态

### 使用xml创建动画
1. 创建包`res/anim`
2. 创建xml资源文件.可以有的根结点:
	1. set:动画集合:可以内嵌set,同过`startOffset`调整先后顺序
	2. scale:缩放

		属性
		1. `android:duration="3000"`
		2. `android:fromXScale="1"` Y:表示动画开始的时候x/y方向控件的尺寸是原x/y方向尺寸的倍数
		3. `android:pivotX="50%"`	Y:锚点,表示缩放的原点,`%`表示占源控件尺寸的比例
		4. `android:toXScale="2"`	Y:表示动画结束的时候x/y方向控件的尺寸是原x/y方向尺寸的倍数

	3. rotate

		属性
		1. `android:fromDegrees="0"`:开始角度
        	2. `android:pivotX="50%"`:锚点
        	3. `android:pivotY="50%"`
        	4. `android:toDegrees="45"`:结束角度
	4. alpha:0表示全透明

		属性
		1. `android:fromAlpha="0"`
		2. `android:toAlpha="1"`
		5. translate

		属性:默认原点是控件的左上角
		1. `android:fromXDelta="0"`:用百分比,表示和控件尺寸比较的比例
        	2. `android:fromYDelta="0"`
	        3. `android:toXDelta="50%"`
	        4. `android:toYDelta="50%"`


	几个通用属性:
	1. `android:duration="2000"`
	2. `android:startOffset="700"`:表示延迟时间ms
	3. `android:fillBefore="true"`动画结束的时候停在第一帧
	4. `android:fillAfter="true"`:动画结束的时候停在最后一帧
	5. `android:interpolator="@android:anim/accelerate_decelerate_interpolator"`

3. `Animation a = AimationUtils.loadAnimation(Context context, @AnimRes int id)`
4. 控件`.startAnimation(a)`

### 使用纯code实现动画
![Animation](http://i.imgur.com/KVJcwsN.png)

1. 创建`Animation`子类对象
2. 使用这个对象设置属性
3. 控件调用`startAnimation(..Animation对象)`

>和Animation相关的api用于补间动画,`AnimationSet`

### AnimationSet的使用
1. 创建`AnimationSet`对象

		AnimationSet animationSet = new AnimationSet(true);
2. 添加子动画`Animation`对象

		animationSet.addAnimation(mTranslateAnimation);
    	animationSet.addAnimation(mRotateAnimation);
3. 控件对象调用：`.start()`方法

例子：

```java
    public static void slideInRight(View view) {
        view.setVisibility(View.VISIBLE);

        view.post(() -> {
            int width1 = view.getWidth();
            TranslateAnimation translateAnimation = new TranslateAnimation(width1, 0, 0, 0);
            translateAnimation.setDuration(500);
            view.startAnimation(translateAnimation);
        });
    }
```


## 属性动画

### 特点
- 改变了控件的属性

### 属性动画工作方式
![http://img.blog.csdn.net/20140720154340156](http://img.blog.csdn.net/20140720154340156)

*ValueAnimator:*属性动画的核心类,记录动画的一些属性(动画显示时间,开始值,结束值,计算插值因子的TimeInterpolator,定义属性计算方式的TypeEvaluator)

### ValueAnimator

#### Animator 监听器介绍

AnimatorListener:

- onAnimationStart();
- onAnimationEnd();
- onAnimationRepeat();
- onAnimationCancel(); 动画取消后会调用 end

AnimatorUpdateListener:

- onAnimationUpdate():动画每播放一帧时调用, 在动画过程中,在这个方法中通过传入的ValueAnimator计算属性值(ValueAnimator.getAnimatedValue()),如果使用ValueAnimator来实现动画必须实现这个监听器.

#### 自定义Interpolator (插值器)
定义:动画改变过程中属性变化规则, 插值器它定义了动画变化过程中的属性变化规则，它根据时间比例因子计算出一个插值因子

android中有实现好的插值器,可以适用于大部分应用场景.

自定义Interpolator,返回插值因子

	public class CustomInterpolator implements TimeInterpolator {  

	    @Override  
	    public float getInterpolation(float input) {  
		// 编写相关的逻辑计算  
		//input *= 0.8f;  
		return input * input;  
	    }  
	}  

#### 自定义TyperEvaluator
根据插值因子计算属性值

- IntEvaluator
- FloatEvaluator
- ArgbEvalutor

		@Override
		public Number evaluate(float fraction, Number startValue, Number endValue) {
			float propertyResult = 0;
			/*float startFloat = startValue.floatValue();
			return (startFloat + fraction * (endValue.floatValue() - startFloat));*/
			return propertyResult;
		}

#### 使用ValueAnimator步骤

1. 调用ValueAnimation类中的静态方法(ofInt,ofFloat,...)创建`ValueAnimation`对象
2. 调用addUpdateListener(AnimatorUpdateListener mListener)方法为ValueAnimator对象设置属性变化的监听器;
3. 创建自定义的Interpolator，调用setInterpolator(TimeInterpolator value)为ValueAniamtor设置自定义的Interpolator;(可选，不设置默认为缺省值)
4. 创建自定义的TypeEvaluator,调用setEvaluator(TypeEvaluator value)为ValueAnimator设置自定义的TypeEvaluator;(可选，不设置默认为缺省值)
5. 在AnimatorUpdateListener 中的实现方法为目标对象的属性设置计算好的属性值。
6. 设置动画的持续时间、是否重复及重复次数等属性;
7. 为ValueAnimator设置目标对象并开始执行动画。


### ObjectAnimator
是ValueAnimator的子类, 继承了ValueAnimator的所有方法和特性,并且封装了很多实用的方法,方便快速实现动画.

属性值会自动更新, 不用实现ValueAnimator.AnimatorUpdateListener

动画:

1. translationX 和 translationY
2. rotation、rotationX 和 rotationY
3. scaleX、scaleY
4. pivotX 和 pivotY
5. x 和 y:指 View 在容器内的最终位置，等于 View 左上角相对于容器的坐标加上 translationX 和 translationY 后的值;
6. alpha:1 不透明, 0 透明

#### 使用步骤
1. 调用静态方法 ofFloat(),ofInt... 创建ObjectAnimator对象,并设置View对象,改变的属性名,初始值,结束值.
2. 设置动画持续时间,是否重复,重复次数.
3. 启动动画.



### 创建属性动画

![Animator继承图](http://i.imgur.com/fsJHN5g.png)

#### 1 使用`ValueAnimator`

>设置单个动画

#### 2 使用`ObjectAnimator`

1. 使用静态方法`ObjectAnimator objectAnimator = ObjectAnimator.ofInt(text,"textColor",0xffff0000,0xffff00ff);`返回的是animatior对象，这里设置的是控件需要改变的属性
	1. ofInt
	2. ofFloat
	3. ofObject
	4. ofArgb
	5. ofMultiInt
	6. ofMultiFloat
	7. ofObject

	**注意**：参数中的属性必须有`setter`方法（**ObjectAnimator是通过调用这个方法来改变对象的属性的，`ValueAnimator`不是**）

	需要设置的属性没有`setter`的解决办法：
	- 添加`setter`方法,如果你有权限的话
	- 创建一个包装类,创建`setter`方法
	- 使用`ValueAnimator`代替

	**扩展**
	- 如果只设置了一个属性`values..`这个默认作为结束值,所以Object必须有`getter`方法来获取开始值
	- `getter`和`setter`方法获取的数值类型必须是一样的
	- 有时候改变一些特别属性的时候需要使用`invalidate()`方法强行让View在屏幕上重绘,这个方法在`onAnimationUpdate()`方法内使用.
2. 设置动画的其他属性
	1. `objectAnimator.setDuration(3000);`。。。

3. `objectAnimator.start();`



自定义 View 的时候通常可以使用这个。

```java
public class SportsView extends View {  
     float progress = 0;

    ......

    // 创建 getter 方法
    public float getProgress() {
        return progress;
    }

    // 创建 setter 方法
    public void setProgress(float progress) {
        this.progress = progress;
        invalidate();
    }

    @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        ......

        canvas.drawArc(arcRectF, 135, progress * 2.7f, false, paint);

        ......
    }
}

......

// 创建 ObjectAnimator 对象
ObjectAnimator animator = ObjectAnimator.ofFloat(view, "progress", 0, 65);  
// 执行动画
animator.start();  
```



#### 3 使用`TimeAnimator`
>TimeAnimator:提供了一个简单的回调机制，通过 TimeAnimator.TimeListener，在动画的每一帧处通知你。


#### 4 使用`ViewPropertyAnimator` 最简单，最常用

1. 控件对象`.animator()`返回一个`ViewPropertyAnimator`对象

		ViewPropertyAnimator animate = image.animate();
2. `ViewPropertyAnimator`可以设置各种动画(构建者模式)
3. `.start()`开始动画

**兼容方法**
1. ` ViewPropertyAnimator animate = ViewCompat.animate(控件对象)`
2. 正常使用`animate`

**注意：**做需要锚点的动画的时候可以设置控件的锚点：

	btn.setPivotX(btn.getWidth() / 2);
	btn.setPivotY(btn.getHeight() / 2);
    
#### 5 使用`AnimatorSet`


1. `AnimatorSet set = new AnimatorSet();`构建对象
2. `.playTogether(。。)`相关方法，加入Animator对象:**注意**Animator的对象要相同
3. `set.start()`

## 扩展

### 删除一个控件的操作

1. 伪删除
	1. `setVisibility(..)`

2. 真删除
	1. `ViewGroup vg = (ViewGroup) imageView.getParent();`
	2. `vg.removeView(imageView)`

3. View
	1. `public boolean post(Runnable action)`
	2. `public boolean postDelayed(Runnable action, long delayMillis)`


参考:[http://blog.csdn.net/yegongheng/article/details/38397725](http://blog.csdn.net/yegongheng/article/details/38397725)
