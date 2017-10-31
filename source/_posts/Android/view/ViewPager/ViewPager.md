---
title: ViewPager
date: 2016-03-23 12:38:14
tags: [ViewPager, View]
categories: Android

---

>android.support.v4.view.ViewPager 不在Android原生包中

# ViewPager 类

# 适配器PagerAdapter
>不常用,只适合简单的布局

<!--more-->
## 创建这个适配器需要的属性
- 数据源: `private List<String> list;`
- 池子存放View: `private Pools.Pool<View> viewPool;`

	作用:

		设置控件池,减少控件的创建次数,复用池中的控件(前提是页面布局相同可以复用)

	池子可以在构造方法中进行初始化:

		viewPool = new Pools.SimplePool<View>(3);
	池子的简单操作:

	1. 取

			viewPool.acquire()
	2. 放回

			viewPool.release(((TextView) object));

	池子源码中的介绍用法是:单例设计模式


		public class MyPooledClass {

	    	private static final SynchronizedPool<MyPooledClass> sPool =
	              new SynchronizedPool<MyPooledClass>(10);

	    	public static MyPooledClass obtain() {
	          MyPooledClass instance = sPool.acquire();
	          return (instance != null) ? instance : new MyPooledClass();
	    	}

	    	public void recycle() {
	           // Clear state if needed.
	           sPool.release(this);
		}
	     . . .

## 创建这个适配器构造方法
    public MyPagerAdapter(Context context, List<String> list) {
        this.context = context;
        this.list = list;
        viewPool = new Pools.SimplePool<View>(3);
    }
## 创建这个适配器的子类必须重写的方法：
- ` public int getCount()`
- `public boolean isViewFromObject(View view, Object object)`

	作用:

	1. 判断`View`(item)页面视图是否和`Key`关联了
	2. 参数2:`instantiateItem`方法返回的`Object`
		1. 如果`Object`是数据,方法里面的代码就是:

				Object vo = view.getTag();
				return vo.equals(object);
		2. 如果`Object`是View对象,方法里面的代码就是:

				return view == object;
- `public Object instantiateItem(ViewGroup container, int position)`：必须去掉`super`

	作用:

	1. 初始化item,(可能需要从Pool中取)
	2. 并将item加入到`ViewGroup`中;
	3. 返回`Object`:
		1. 数据：setTag(数据) ，返回数据；
		2. item的布局View
- `public void destroyItem(ViewGroup container, int position, Object object)`：必须去掉`super`

	作用:

	1. 在这个方法内把`View`从`ViewGroup`中移除
	2. 如果前面使用返回数据方式（Tag）

			if (object != null) {
           		 View v = container.findViewWithTag(object);
           		 container.removeView(v);
          	 	 mViewPool.release(v);
       		}

## 其他方法：

# 子类适配器FragmentPagerAdapter

## 准备Fragment ， 创建一个Fragment的子类
- 无参构造方法
- newInstance(。。)方法可以传入参数，

		public static GalleryFragment newInstance(int id, String title) {

        	Bundle args = new Bundle();
        	args.putInt("id", id);
        	args.putString("title", title);
        	GalleryFragment fragment = new GalleryFragment();
        	fragment.setArguments(args);
        	return fragment;
    	}

- `public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)`：在这个方法内创建fragment整个布局视图
- `public void onViewCreated(View view, @Nullable Bundle savedInstanceState)`：在这里给fragment布局视图中的布局加载数据，这是`Adapter`

## FragmentPagerAdapter子类要写的方法
- 构造方法：在构造方法中传入数据源
- `public Fragment getItem(int position)`：根据数据源创建`Fragment`对象。
- `public int getCount()`：页数

## activity
- 在activity中获取`ViewPager`对象
- 创建数据源
- 创建`FragmentPagerAdapter`适配器，参数：`FragmentManager`和数据源
- `ViewPager`对象设置适配器


# 子类适配器FragmentStatePagerAdapter
>在有大量页面的时候使用，更省内存使用方法和`FragmentPagerAdapter`类似

# ViewPager监听器
- setOnPageChangeListener:@deprecated
- addOnPageChangeListener


# ViewPager轮播效果
## 伪无限循环
>不需要监听器

关键代码：

	@Override
    public int getCount() {
        return Integer.MAX_VALUE;
    }

	image.setImageResource(((int) mMaps.get(position % mMaps.size()).get("imageId")));

	// 在Activity中设置pager到中间并保证是第一页
	pager.setCurrentItem(Integer.MAX_VALUE / 2 / mMaps.size() * mMaps.size());

## 头尾跳动
将尾页面复制一份加载第一页,将头页面复制一份加载最后一页

	1 2 3 4 5 --> 5 1 2 3 4 5 1
当页面滑到最后个1时跳动页面到前面的1，当页面滑到前面的5时页面跳动到后面的5，因为页面相同直接跳动是看不出来的。

关键代码：

适配器：

	public MyPagerAdapter(Context context, List<String> list) {
        this.context = context;
        this.list = list;
        String first = list.get(0);
        String last = list.get(list.size() - 1);
        list.add(0, last);
        list.add(first);
        viewPool = new Pools.SimplePool<View>(4);
    }


监听器：

	    @Override
    public void onPageSelected(int position) {
        if (position == list.size() - 1) {
            pager.setCurrentItem(1,false);
        }
        if (position == 0) {
            pager.setCurrentItem(list.size() - 2,false);
        }
    }

# 重要的滑动监听

ViewPager.OnPageChangeListener

## 方法

1. `public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels)`

调用时间：滚动过程中

参数：(和国家习惯有关系，有的国家习惯从右往左)1. 左侧的页面位置(position小的)；2.  偏移量，右侧(position大的)在屏幕中的占比； 3. 偏移像素

作用：可以在这方法中利用偏移参数做一些滑动动画


2. `public void onPageSelected(int position)`
调用时间：滚动完成后调用，代表我们选择了这个item

3. `public void onPageScrollStateChanged(int state)`

调用时间：ViewPager状态改变的时候


# ViewPager 轮播定时器


## 导入定时相关代码

    // 定时
    private ScheduledExecutorService scheduledExecutorService;

    private void startPlay() {
        scheduledExecutorService = Executors.newSingleThreadScheduledExecutor();
        scheduledExecutorService.scheduleAtFixedRate(new SlideShowTask(), 1, 4, TimeUnit.SECONDS);
    }

    /**
     * 停止轮播图切换
     */
    private void stopPlay() {
        scheduledExecutorService.shutdown();
    }

    /**
     * 执行轮播图切换任务
     *
     * @author caizhiming
     */
    private class SlideShowTask implements Runnable {

        @Override
        public void run() {
            synchronized ("") {
                mHandler.obtainMessage().sendToTarget();
            }
        }

    }

## 需要让ViewPager开始轮播的时候调用start
>比如：当ViewPager作为RecycleView的头的时候，在recyclerView的构造方法中调用下面的方法
    startPlay();

### 在 onBindViewHolder 方法中需要初始化position
>或者使用单例模式来防止ViewPager的销毁和创建
    mPagePos = 0;


# 使用代码添加 ViewPager 指示器例子
>在ViewPager的布局上覆盖一个 LinearLayout 作为指示器的容器

    mCircleImages = new View[size];
    for (int i = 0; i < focusImages.getList().size(); i++) {
        ImageView imageView = new ImageView(mContext);
        LinearLayout.LayoutParams params = new LinearLayout.LayoutParams((int) (6 * density), (int) (6 * density));
        params.setMargins((int) (6 * density), 0, (int) (6 * density), 0);
        imageView.setLayoutParams(params);
        if (i == 0) {
            imageView.setBackgroundResource(R.drawable.viewpager_cycle_black);
        } else {
            imageView.setBackgroundResource(R.drawable.viewpager_cycle);
        }
        mCircleImages[i] = imageView;
        mLinearLayout.addView(imageView);
    }

下面的代码可优化

        @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
        for (int j = 0; j < mCircleImages.length; j++) {
            if (j == position % size) {
                mCircleImages[j].setBackgroundResource(R.drawable.viewpager_cycle);
            } else {
                mCircleImages[j].setBackgroundResource(R.drawable.viewpager_cycle_black);
            }
        }

        //设置全局变量，currentIndex为选中图标的 index
        //mPagePos = position;
    }




## 禁止ViewPager滑动

>@Override

>public boolean onTouchEvent(MotionEvent ev) {

>return false;

>@Override

>public boolean onInterceptTouchEvent(MotionEvent ev) {

>return false;


# 导航

### GridButton 作为导航


# 设置缓存页面

	viewPager.setOffscreenPageLimit(cachePagers);// 设置缓存页面


# 切换动画实现


	mVP.setPageTransformer(false, new ViewPager.PageTransformer() {
		@Override
		public void transformPage(View page, float position) {
			//page参数代表当前view 或 fragment，position参数就是它的位置的值。
			// 滑动的时候，起始page和目标page的各自的transformPage()就会被同时触发调用。
			//position是一个page相对于屏幕中心的位置。
			//当page填充屏幕完全可见的时候，它的position是0, page位于屏幕右边，它的position是1。page位于屏幕左边，它的position是-1。


			// （因为左右是对称的）所以，为了不考虑正负值，我们取position的绝对值：
			final float abs = Math.abs(Math.abs(position) - 1);

			//淡入淡出效果
	//                page.setAlpha(abs);

			//尺寸大小变化效果

			page.setScaleX(abs / 2 + 0.5f);
			page.setScaleY(abs / 2 + 0.5f);

			//使page沿Z轴方向旋转30度
	//                page.setRotationY(position * -30);
		}
	});


# 现实现实多个页面

参考：[http://blog.csdn.net/jm_beizi/article/details/51297605](http://blog.csdn.net/jm_beizi/article/details/51297605)

## 0 设置 android:clipChildren属性为”false”.

`android:layerType=”software”` 可能需要

>注意：setClipChildren(false)在3.0以上版本中，开启了硬件加速后将不能正常工作，所以需要将其设置为软件加速。设置软硬件加速使用 setLayerType(View.LAYER_TYPE_SOFTWARE, null); 也可以在布局文件中添加 android:layerType=”software”

```xml
<RelativeLayout
    android:id="@+id/viewPager_container"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:background="@android:color/white"
    android:clipChildren="false"
    android:layerType="software">

    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginLeft="110dp"
        android:layout_marginRight="110dp"
        android:clipChildren="false" />
</RelativeLayout>
```


## 1 设置 ViewPapger 缓存页数

```java
mViewPager.setOffscreenPageLimit(2);  // 建议 2
```

## 2 设置页与页之间的间距 （可跳过）

```java
mViewPager.setPageMargin(int marginPixls);  // setPageMargin表示设置page之间的间距
```

## 3 设置滑动，点击事件


