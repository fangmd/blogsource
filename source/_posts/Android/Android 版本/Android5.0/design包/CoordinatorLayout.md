---
title: CoordinatorLayout/design包
date: 2016-04-13 14:38:14
tags: [CoordinatorLayout, Android5.0]
categories: android

---

# 导包

	compile 'com.android.support:design:23.3.0'

# 特有属性
- layout_behavior

# behavior

CoordinatorLayout 通过 Behavior 协调里面的之布局

<!--more-->

## Child，Dependency

Child 控件根据其 Dependency 控件的变化而变化


## 自定义一个 Behavior

实现一个类继承 `CoordinatorLayout.Behavior<T>`

实现下面两个方法：


	/**
	 * Determine whether the supplied child view has another specific sibling view as a
	 * layout dependency.
	 *
	 * <p>This method will be called at least once in response to a layout request. If it
	 * returns true for a given child and dependency view pair, the parent CoordinatorLayout
	 * will:</p>
	 * <ol>
	 *     <li>Always lay out this child after the dependent child is laid out, regardless
	 *     of child order.</li>
	 *     <li>Call {@link #onDependentViewChanged} when the dependency view's layout or
	 *     position changes.</li>
	 * </ol>
	 *
	 * @param parent the parent view of the given child
	 * @param child the child view to test
	 * @param dependency the proposed dependency of child
	 * @return true if child's layout depends on the proposed dependency's layout,
	 *         false otherwise 返回 true 表示 child 依赖 dependency，false 不依赖
	 *
	 * @see #onDependentViewChanged(CoordinatorLayout, android.view.View, android.view.View)
	 */
	public boolean layoutDependsOn(CoordinatorLayout parent, V child, View dependency) {
		boolean ret;
		// 更具逻辑判断 ret 的取值
		return ret;
	}


	/**
	 * Respond to a change in a child's dependent view
	 *
	 * <p>This method is called whenever a dependent view changes in size or position outside
	 * of the standard layout flow. A Behavior may use this method to appropriately update
	 * the child view in response.</p> 当dependency发生改变时（位置、宽高等），执行这个函数
	 *  
	 * <p>A view's dependency is determined by
	 * {@link #layoutDependsOn(CoordinatorLayout, android.view.View, android.view.View)} or
	 * if {@code child} has set another view as it's anchor.</p>
	 *
	 * <p>Note that if a Behavior changes the layout of a child via this method, it should
	 * also be able to reconstruct the correct position in
	 * {@link #onLayoutChild(CoordinatorLayout, android.view.View, int) onLayoutChild}.
	 * <code>onDependentViewChanged</code> will not be called during normal layout since
	 * the layout of each child view will always happen in dependency order.</p>
	 *
	 * <p>If the Behavior changes the child view's size or position, it should return true.
	 * The default implementation returns false.</p>
	 *
	 * @param parent the parent view of the given child
	 * @param child the child view to manipulate
	 * @param dependency the dependent view that changed
	 * @return true if the Behavior changed the child view's size or position, false otherwise 返回true表示child的位置或者是宽高要发生改变，否则就返回false
	 */
	public boolean onDependentViewChanged(CoordinatorLayout parent, V child, View dependency) {
		return false;
	}



## behavior设置方式

以 SwipeDismissBehavior 为例子

**注意：只有 CoordinatorLayout 直接之 View 才有效**

### 方法一:布局中设置

	xmlns:app="http://schemas.android.com/apk/res-auto"

需要behavior实现构造方法：

	public class MyBehavior extends SwipeDismissBehavior {

	    public MyBehavior(Context context, AttributeSet sttrs) {

	    }
	}

报错：官方`SwipeDismissBehavior`类没有实现布局文件创建对象的时候的构造方法。

	app:layout_behavior="android.support.design.widget.SwipeDismissBehavior"

可行：

	app:layout_behavior="com.doublefang.designdemo.MyBehavior"

没有提示

### 方法二：代码设置


    TextView text = (TextView) findViewById(R.id.main_text);
    CoordinatorLayout.LayoutParams layoutParams = (CoordinatorLayout.LayoutParams) text.getLayoutParams(); // 默认是viewgroup
    SwipeDismissBehavior swipeDismissBehavior = new SwipeDismissBehavior();
    swipeDismissBehavior.setListener(new SwipeDismissBehavior.OnDismissListener() {
        @Override
        public void onDismiss(View view) {
            // 让控件真正消失
            final CoordinatorLayout parent = (CoordinatorLayout) view.getParent();
        	parent.removeView(view);
        	ViewCompat.setAlpha(view, 1);
        	ViewCompat.setTranslationX(view, 0);
        	Snackbar.make(mLayout, "删除", Snackbar.LENGTH_SHORT)
                	.setAction("撤销", new View.OnClickListener() {
                    	@Override
                    	public void onClick(View v) {
                    	    parent.addView(mText);
                    	}
                	})
                	.show();
        }

        @Override
        public void onDragStateChanged(int state) {

        }
    });
    layoutParams.setBehavior(swipeDismissBehavior);

# 联合使用的控件

## Snackbar

    Snackbar.make(mLayout, "删除", Snackbar.LENGTH_SHORT)
            .setAction("撤销", new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    parent.addView(mText);
                }
            })
            .setCallback(new Snackbar.Callback() {
                @Override
                public void onDismissed(Snackbar snackbar, int event) {
                    super.onDismissed(snackbar, event);
                    if (event != Snackbar.Callback.DISMISS_EVENT_ACTION) {
                        mText = null;
                    }
                }

                @Override
                public void onShown(Snackbar snackbar) {
                    super.onShown(snackbar);
                }
            })
            .show();

## FloatingActionButton
>和普通button的用法一样，

	<android.support.design.widget.FloatingActionButton
	        android:id="@+id/main_btn"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:layout_gravity="bottom|right"
	        android:layout_margin="20dp"
	        android:src="@mipmap/ic_launcher"/>

。

	mBtn = (FloatingActionButton) findViewById(R.id.main_btn);
	mBtn.setOnClickListener(this);

## AppBarLayout

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?actionBarSize"
            app:layout_scrollFlags="scroll|enterAlways"
            app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            >
        </android.support.v7.widget.Toolbar>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="#ccc"
            android:text="不会消失可以设置导航"/>

    </android.support.design.widget.AppBarLayout>

属性：

1. `app:layout_scrollFlags="scroll|enterAlways"`一旦向上滚动这个view就可见。
2. `app:layout_scrollFlags="scroll|enterAlwaysCollapsed"`这个flag定义的是何时进入（已经消失之后何时再次显示）。假设你定义了一个最小高度（minHeight）同时enterAlways也定义了，那么view将在到达这个最小高度的时候开始显示，并且从这个时候开始慢慢展开，当滚动到顶部的时候展开完。
3. `app:layout_scrollFlags="scroll|exitUntilCollapsed`: 这个flag定义何时退出，当你定义了一个minHeight，这个view将在滚动到达这个最小高度的时候消失。


### Toolbar 解决左边空格
添加下面属性：或者设置style

	app:contentInsetLeft="0dp
	app:contentInsetStart="0dp"

## 两个可协调的滑动控件

### RecyclerView

	app:layout_behavior="@string/appbar_scrolling_view_behavior"

### NestedScrollView
>代替ScrllView，他能和CoordinatorLayout配合

	app:layout_behavior="@string/appbar_scrolling_view_behavior"

## CollapsingToolbarLayout 折叠布局
用来对 Toolbar 进行再次包装的 ViewGroup，并且作为 AppBarLayout 的直接子 View


    <android.support.design.widget.CollapsingToolbarLayout
        android:id="@+id/collapsing_toolbar_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:contentScrim="#30469b"
        app:layout_scrollFlags="scroll|exitUntilCollapsed">

        <ImageView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:scaleType="centerCrop"
            android:src="@mipmap/ic_launcher"
            app:layout_collapseMode="parallax"
            app:layout_collapseParallaxMultiplier="0.7"/>

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_collapseMode="pin"/>
    </android.support.design.widget.CollapsingToolbarLayout>


作用：

1. 折叠Title（Collapsing title）：当布局内容全部显示出来时，title 是最大的，但是随着 View 逐步移出屏幕顶部，title 变得越来越小。你可以通过调用 setTitle 函数来设置 title。
2. 内容纱布（Content scrim）：根据滚动的位置是否到达一个阀值，来决定是否对View“盖上纱布”。可以通过 setContentScrim(Drawable) 来设置纱布的图片.
3. 状态栏纱布（Status bar scrim)：根据滚动位置是否到达一个阀值决定是否对状态栏“盖上纱布”，你可以通过 setStatusBarScrim(Drawable) 来设置纱布图片，但是只能在 LOLLIPOP 设备上面有作用。
4. 视差滚动子View(Parallax scrolling children):子View可以选择在当前的布局当时是否以“视差”的方式来跟随滚动。（PS:其实就是让这个View的滚动的速度比其他正常滚动的View速度稍微慢一点）。将布局参数 app:layout_collapseMode设为parallax
5. 将子View位置固定(Pinned position children)：子View可以选择是否在全局空间上固定位置，这对于Toolbar来说非常有用，因为当布局在移动时，可以将Toolbar固定位置而不受移动的影响。


### 定制折叠效果

	mAppBarLayout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
		@Override
		public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
			// 通过这里的 verticalOffset 滑动值 改变布局
			Log.d(TAG, "onOffsetChanged: " + verticalOffset);
			if (verticalOffset <= -mCollapsingToolbarLayout.getHeight() / 2) {

			} else {

			}
		}
	});


### 注意点
1. 使用CollapsingToolbarLayout必须把title设置到CollapsingToolbarLayout上，设置到Toolbar上则不会显示

		// 需要设置 toolbar 高度， wrap_content 不会显示
        mCollapsingToolbarLayout.setTitle("CollapsingToolbarLayout");

        //通过CollapsingToolbarLayout修改字体颜色
        mCollapsingToolbarLayout.setExpandedTitleColor(Color.WHITE);//设置还没收缩时状态下字体颜色
        mCollapsingToolbarLayout.setCollapsedTitleTextColor(Color.GREEN);//设置收缩后Toolbar上字体的颜色

### 属性介绍

`app:contentScrim="#30469b"`:表示折叠后，显示的背景色

`app:layout_collapseMode`有三个选项：

- pin:代表不会折叠，会一直显示
- parallax:会被折叠
- none:和parallax的效果一样

`app:expandedTitleMarginStart="48dp"`:展开后标题距离左边缘的距离

`app:layout_collapseParallaxMultiplier="0.7"`:设置视差滚动因子



## NavigationView


## TextInputLayout

## TabLayout

一些属性：

	app:paddingStart="10dp"
	app:tabIndicatorColor="#5b5bea"
	app:tabIndicatorHeight="3dp"
	app:tabPaddingBottom="4dp"
	app:tabPaddingEnd="10dp"
	app:tabSelectedTextColor="#5b5bea"
