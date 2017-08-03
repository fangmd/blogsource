---
title: Material Design 
date: 2016-11-04 22:38:14
tags: [Material Design]
categories: Android

---



来源：官方文档

# 入门指南

[入门指南](https://developer.android.com/training/material/get-started.html)

如果要使用 Material Design 创建应用需要做到以下几点：

1. 请查阅 [Material Design 规范](http://www.google.com/design/spec)。
2. 在您的应用中使用材料主题。
3. 遵循 Material Design 指导方针创建您的布局。
4. 指定您视图要投射阴影的高度。
5. 使用系统小组件呈现列表与卡片。
6. 定制您的应用中的动画。

# 使用材料主题

> 注意：材料主题仅在 Android 5.0（API 级别 21）及更高版本中提供。
> 如果 Activity 继承自 AppCompatActivity， 就必须使用兼容主题 Theme.AppCompat.Light.NoActionBar

新材料主题提供的：

1. 让您设置主题配色工具的系统小组件
2. 适用于系统小组件的触摸反馈动画
3. 操作行为转换动画

查看 [ R.style](https://developer.android.com/reference/android/R.style.html) 了解更多关于主题的细节。

## 定制配色

    <resources>
      <!-- inherit from the material theme -->
      <style name="AppTheme" parent="android:Theme.Material">
        <!-- Main theme colors -->
        <!--   your app branding color for the app bar -->
        <item name="android:colorPrimary">@color/primary</item>
        <!--   darker variant for the status bar and contextual app bars -->
        <item name="android:colorPrimaryDark">@color/primary_dark</item>
        <!--   theme UI controls like checkboxes and text fields -->
        <item name="android:colorAccent">@color/accent</item>
      </style>
    </resources>

## 定制状态栏

### 方式一 设置属性

在扩展材料主题时使用 android:statusBarColor 属性设置状态栏颜色

让状态栏透明可以设置值为：`@android:color/transparent`

>默认情况下，android:statusBarColor 将继承 android:colorPrimaryDark 的值。

## 方式二

    Window.setStatusBarColor()

## 给 Activity 设置不用的主题

    XML 布局定义内的元素可指定 android:theme

## 布局设计

[Material Design 指导方针](http://www.google.com/design/spec)

需要注意的点：

1. 基线格点
2. 关键线
3. 间距
4. 触摸目标大小
5. 布局结构

## 指定视图的高度

设置属性： 'android:elevation'

属性：'translationZ' 可以创建反映出视图高度临时变化的动画，高度变化可在[响应触摸手势](https://developer.android.com/training/material/animations.html#ViewState)时发挥作用。

更多相关：[定义阴影与裁剪视图](https://developer.android.com/training/material/shadows-clipping.html)


# 创建列表与卡片

使用 `RecyclerView`, `CardView`

## RecyclerView 使用

### 优化

    // use this setting to improve performance if you know that changes
    // in content do not change the layout size of the RecyclerView
    mRecyclerView.setHasFixedSize(true);

### 设置动画

扩展 RecyclerView.ItemAnimator 类别并使用 RecyclerView.setItemAnimator() 方法。

## CardView

扩展自 FrameLayout

- 设置阴影：`card_view:cardElevation` (在 Android 5.0（API 级别 21）及更高版本中使用真实高度与动态阴影，而在早期的 Android 版本中则返回编程阴影实现。)

- 设置圆角： `card_view:cardCornerRadius`, `CardView.setRadius`
- 背景色：`card_view:cardBackgroundColor`


# 定义阴影与裁剪视图

由 Z 属性所表示的视图高度将决定其阴影的视觉外观：拥有较高 Z 值的视图将投射更大且更柔和的阴影。 拥有较高 Z 值的视图将挡住拥有较低 Z 值的视图；不过视图的 Z 值并不影响视图的大小。


## 指定视图高度

- 高度：静态组件。
- 转换：用于动画的动态组件

Z = elevation + translationZ

定义视图高度： `android:elevation`, `View.setElevation()`

设置视图转换，请使用 View.setTranslationZ() 方法

全新的 ViewPropertyAnimator.z() 与 ViewPropertyAnimator.translationZ() 方法让您能够轻松地为视图高度添加动画。如果要了解更多信息，请参阅 ViewPropertyAnimator 的 API 参考以及属性动画开发者指南。

Z 值以 dp（密度独立像素）为单位度量

####  各个控件应该设置的高度

![Material_design 各个控件的高度](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/material_design.png)


## 自定义视图阴影与轮廓

视图的背景可绘制对象的边界将决定其阴影的默认形状。

背景可绘制对象被定义为一个拥有圆角的矩形：

    <!-- res/drawable/myrect.xml -->
    <shape xmlns:android="http://schemas.android.com/apk/res/android"
           android:shape="rectangle">
        <solid android:color="#42000000" />
        <corners android:radius="5dp" />
    </shape>

视图将投射一个带有圆角的阴影，因为背景可绘制对象将定义视图的轮廓。

## 裁剪视图

`View.setClipToOutline()`, `android:clipToOutline`

# Working with drawable

- 图片着色
- 突出颜色萃取
- 矢量图片

## 为图片资源着色

`android:tint `, `android:tintMode`, `setTint()` 为  BitmapDrawable 或 NinePatchDrawable 对象着色

## 从图像萃取突出颜色

Android 支持内容库 r21 及更高版本包括 Palette 类别，可让您从图像萃取突出颜色。

萃取下列突出颜色:

- 鲜艳
- 鲜艳深色
- 鲜艳浅色
- 低调
- 低调深色
- 低调浅色

usage: 将 Bitmap 对象传递给位于您上载图像的背景线程的 Palette.generate() 静态方法。 如果您无法使用此线程，请调用 Palette.generateAsync() 方法并提供一个侦听程序。

    dependencies {
        ...
        compile 'com.android.support:palette-v7:21.0.0'
    }

# 定制您的动画 Defining Custom animations

 Android 5.0（API 级别 21）及更高版本可让您定制这些动画，同时也可创建新动画：

- 触摸反馈
- 循环揭露
- 操作行为转换
- 曲线运动
- 视图状态改变

## 定制触摸反馈

利用 RippleDrawable 实现触摸波纹效果

- ?android:attr/selectableItemBackground 指定有界的波纹。
- ?android:attr/selectableItemBackgroundBorderless 指定越过视图边界的波纹

改变默认触摸反馈颜色，请使用主题的 android:colorControlHighlight 属性

## 使用揭露效果

ViewAnimationUtils.createCircularReveal() 方法让您能够为裁剪区域添加动画以揭露或隐藏视图。


显示view

    final TextView tv9 = (TextView) findViewById(R.id.tv9);

    findViewById(R.id.content_main).setOnClickListener(new View.OnClickListener() {
        @Override public void onClick(View v) {
            // get the center for the clipping circle
            int cx = (tv9.getRight() - tv9.getLeft()) / 2;
            int cy = (tv9.getBottom() - tv9.getTop()) / 2;

            // get the final radius for the clipping circle
            int finalRadius = Math.max(tv9.getWidth(), tv9.getHeight());

            // create the animator for this view (the start radius is zero)
            final Animator anim = ViewAnimationUtils.createCircularReveal(tv9, cx, cy, 0, finalRadius);

            tv9.setVisibility(View.VISIBLE);

            anim.start();
        }
    });

隐藏view：

    final TextView tv9 = (TextView) findViewById(R.id.tv9);

    tv9.setOnClickListener(new View.OnClickListener() {
        @Override public void onClick(View v) {
            // get the center for the clipping circle
            int cx = (tv9.getRight() - tv9.getLeft()) / 2;
            int cy = (tv9.getBottom() - tv9.getTop()) / 2;

            // get the final radius for the clipping circle
            int initRadius = Math.max(tv9.getWidth(), tv9.getHeight());

            // create the animator for this view (the start radius is zero)
            final Animator anim = ViewAnimationUtils.createCircularReveal(tv9, cx, cy, initRadius, 0);

            anim.addListener(new AnimatorListenerAdapter() {
                @Override public void onAnimationEnd(Animator animation) {
                    super.onAnimationEnd(animation);
                    // make the view visible and start the animation
                    tv9.setVisibility(View.INVISIBLE);
                }
            });
            anim.start();
        }
    });

沿着中心缩小:

    Animator animator = ViewAnimationUtils.createCircularReveal(view, view.getWidth() / 2, view.getHeight() / 2, view.getWidth(), 0);
    animator.setInterpolator(new LinearInterpolator());
    animator.setDuration(1000);
    animator.start();

从左上角扩展：

    Animator animator = ViewAnimationUtils.createCircularReveal(view,0,0,0,(float) Math.hypot(view.getWidth(), view.getHeight()));
    animator.setDuration(1000);
    animator.start();

## 定制操作行为转换

为进入、退出转换以及操作行为之间的共享元素转换指定定制动画

Android 5.0（API 级别 21）支持这些进入与退出转换：

- 分解 - 从场景中心移入或移出视图。
- 滑动 - 从场景边缘移入或移出视图。
- 淡入淡出 - 通过调整透明度在场景中增添或移除视图。

任何扩展 Visibility 类别的转换均将获得进入或退出转换支持。 如果要了解更多信息，请参阅 Transition 类别的 API 参考文档。

Android 5.0（API 级别 21）也支持这些共享元素转换：

- changeBounds - 为目标视图的布局边界的变化添加动画。
- changeClipBounds - 为目标视图的裁剪边界的变化添加动画。
- changeTransform - 为目标视图的缩放与旋转变化添加动画。
- changeImageTransform - 为目标图像的大小与缩放变化添加动画。

## 指定定制转换

材料主题继承的风格时，使用 android:windowContentTransitions 属性启用窗口内容转换

    <style name="BaseAppTheme" parent="android:Theme.Material">
      <!-- enable window content transitions -->
      <item name="android:windowContentTransitions">true</item>

      <!-- specify enter and exit transitions -->
      <item name="android:windowEnterTransition">@transition/explode</item>
      <item name="android:windowExitTransition">@transition/explode</item>

      <!-- specify shared element transitions -->
      <item name="android:windowSharedElementEnterTransition">
        @transition/change_image_transform</item>
      <item name="android:windowSharedElementExitTransition">
        @transition/change_image_transform</item>
    </style>


change_image_transform:

    <!-- res/transition/change_image_transform.xml -->
    <!-- (see also Shared Transitions below) -->
    <transitionSet xmlns:android="http://schemas.android.com/apk/res/android">
      <changeImageTransform/>
    </transitionSet>

如果要在您的代码中启用窗口内容转换，请调用 Window.requestFeature() 方法：

    // inside your activity (if you did not enable transitions in your theme)
    getWindow().requestFeature(Window.FEATURE_CONTENT_TRANSITIONS);

    // set an exit transition
    getWindow().setExitTransition(new Explode());


如果要在您的代码中指定转换，请以 Transition 对象调用这些方法：

- Window.setEnterTransition()
- Window.setExitTransition()
- Window.setSharedElementEnterTransition()
- Window.setSharedElementExitTransition()

setExitTransition() 和 setSharedElementExitTransition() 方法定义正在调用的操作行为的退出转换。 setEnterTransition() 与 setSharedElementEnterTransition() 方法定义被调用的操作行为的进入转换。

## 使用转换启动一个操作行为

    startActivity(intent,
                  ActivityOptions.makeSceneTransitionAnimation(this).toBundle());

## 以共享元素启动一个操作行为

如果要在两个拥有共享元素的操作行为之间安排屏幕转换动画：

1. 请在您的主题中启用窗口内容转换。
2. 在您的风格中指定一个共享元素转换。
3. 将您的转换定义为 XML 资源。
4. 利用 android:transitionName 属性对两个布局中的共享元素指定一个通用名称。
5. 使用 ActivityOptions.makeSceneTransitionAnimation() 方法。

        // get the element that receives the click event
        final View imgContainerView = findViewById(R.id.img_container);

        // get the common element for the transition in this activity
        final View androidRobotView = findViewById(R.id.image_small);

        // define a click listener
        imgContainerView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent = new Intent(this, Activity2.class);
                // create the transition animation - the images in the layouts
                // of both activities are defined with android:transitionName="robot"
                ActivityOptions options = ActivityOptions
                    .makeSceneTransitionAnimation(this, androidRobotView, "robot");
                // start the new activity
                startActivity(intent, options.toBundle());
            }
        });

对于在您的代码中生成的共享动态视图，请使用 View.setTransitionName() 方法在两个操作行为中指定一个通用元素名称。

如果要在完成第二项操作行为时反转场景转换动画，请调用 Activity.finishAfterTransition() 方法而非 Activity.finish()。

## 以多个共享元素启动一个操作行为

    ActivityOptions options = ActivityOptions.makeSceneTransitionAnimation(this,
            Pair.create(view1, "agreedName1"),
            Pair.create(view2, "agreedName2"));

## 使用曲线运动

PathInterpolator 类别是一个基于贝塞尔曲线或 Path 对象的全新插入器。

    <pathInterpolator xmlns:android="http://schemas.android.com/apk/res/android"
        android:controlX1="0.4"
        android:controlY1="0"
        android:controlX2="1"
        android:controlY2="1"/>

系统将为 Material Design 规范中的三种基本曲线提供 XML 资源：

- @interpolator/fast_out_linear_in.xml
- @interpolator/fast_out_slow_in.xml
- @interpolator/linear_out_slow_in.xml

可以将一个 PathInterpolator 对象传递给 Animator.setInterpolator() 方法。

## 为视图状态改变添加动画

StateListAnimator 类别让您能够定义视图状态改变时运行的动画。

    <!-- animate the translationZ property of a view when pressed -->
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
      <item android:state_pressed="true">
        <set>
          <objectAnimator android:propertyName="translationZ"
            android:duration="@android:integer/config_shortAnimTime"
            android:valueTo="2dp"
            android:valueType="floatType"/>
            <!-- you could have other objectAnimator elements
                 here for "x" and "y", or other properties -->
        </set>
      </item>
      <item android:state_enabled="true"
        android:state_pressed="false"
        android:state_focused="true">
        <set>
          <objectAnimator android:propertyName="translationZ"
            android:duration="100"
            android:valueTo="0"
            android:valueType="floatType"/>
        </set>
      </item>
    </selector>

如果要将定制视图状态动画附加至一个视图，请依照此示例使用 XML 资源文件中的 selector 元素定义一个动画，并使用 android:stateListAnimator 属性将此动画分配给您的视图。

如果要将一个状态列表动画分配给您的代码内的一个视图，请使用 AnimationInflater.loadStateListAnimator() 方法，并以 View.setStateListAnimator() 方法将动画分配给您的视图。

当您的主题扩展材料主题时，在默认情况下按钮将拥有一个 Z 动画。如果要避免您的按钮出现这类行为，请将 android:stateListAnimator 属性设置为 @null。

AnimatedStateListDrawable 类别让您能够创建图片，显示相关视图之间的状态变化。

    <!-- res/drawable/myanimstatedrawable.xml -->
    <animated-selector
        xmlns:android="http://schemas.android.com/apk/res/android">

        <!-- provide a different drawable for each state-->
        <item android:id="@+id/pressed" android:drawable="@drawable/drawableP"
            android:state_pressed="true"/>
        <item android:id="@+id/focused" android:drawable="@drawable/drawableF"
            android:state_focused="true"/>
        <item android:id="@id/default"
            android:drawable="@drawable/drawableD"/>

        <!-- specify a transition -->
        <transition android:fromId="@+id/default" android:toId="@+id/pressed">
            <animation-list>
                <item android:duration="15" android:drawable="@drawable/dt1"/>
                <item android:duration="15" android:drawable="@drawable/dt2"/>
                ...
            </animation-list>
        </transition>
        ...
    </animated-selector>

## 为矢量图片添加动画

 AnimatedVectorDrawable 类别可让您为矢量图片的属性添加动画。

 需要创建 3 个 XML 文件：（它对于 vector 有要求）

 1. 在 res/drawable/ 中拥有 <vector> 元素的矢量图片
 2. 在 res/drawable/ 中拥有 <animated-vector> 元素且已添加动画的矢量图片
 3. 在 res/anim/ 中拥有 <objectAnimator> 元素的一个或多个对象动画

添加动画的矢量图片可为 <group> 以及 <path> 元素的属性添加动画。<group> 元素定义路径集或子组，而 <path> 元素则定义将绘制的路径。

使用 android:name 属性给这些群组和路径指定一个唯一名称，以便让您能够从您的动画定义中引用这些群组或路径

    <!-- res/drawable/vectordrawable.xml -->
    <vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:height="64dp"
        android:width="64dp"
        android:viewportHeight="600"
        android:viewportWidth="600">
        <group
            android:name="rotationGroup"
            android:pivotX="300.0"
            android:pivotY="300.0"
            android:rotation="45.0" >
            <path
                android:name="v"
                android:fillColor="#000000"
                android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
        </group>
    </vector>

已添加动画的矢量图片定义按名称引用矢量图片内的群组和路径：

    <!-- res/drawable/animvectordrawable.xml -->
    <animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
      android:drawable="@drawable/vectordrawable" >
        <target
            android:name="rotationGroup"
            android:animation="@anim/rotation" />
        <target
            android:name="v"
            android:animation="@anim/path_morph" />
    </animated-vector>

动画定义代表着 ObjectAnimator 或 AnimatorSet 对象。

    <!-- res/anim/rotation.xml -->
    <objectAnimator
        android:duration="6000"
        android:propertyName="rotation"
        android:valueFrom="0"
        android:valueTo="360" />

此示例中的第二个动画对矢量图片的路径进行变形:

    <!-- res/anim/path_morph.xml -->
    <set xmlns:android="http://schemas.android.com/apk/res/android">
        <objectAnimator
            android:duration="3000"
            android:propertyName="pathData"
            android:valueFrom="M300,70 l 0,-70 70,70 0,0   -70,70z"
            android:valueTo="M300,70 l 0,-70 70,0  0,140 -70,0 z"
            android:valueType="pathType" />
    </set>

开启动画：

    ImageView iv = (ImageView) findViewById(R.id.iv);
    Drawable drawable = iv.getDrawable();
    if (drawable instanceof Animatable) {
        ((Animatable) drawable).start();
    }

# Maintaining Compatibility

## Define Alternative Styles 定义替换 styles

1. Define a theme that inherits from an older theme (like Holo) in res/values/styles.xml.
2. Define a theme with the same name that inherits from the material theme in res/values-v21/styles.xml.
3. Set this theme as your app's theme in the manifest file.

## Provide Alternative Layouts 提供替换的布局

- res/layout-v21/
- res/layout/

- res/values/
- res/values-v21/

## Use the Support Library 使用支持包

### Dependencies 添加依赖

    dependencies {
        compile 'com.android.support:appcompat-v7:25.0.1'
        compile 'com.android.support:cardview-v7:25.0.1'
        compile 'com.android.support:recyclerview-v7:25.0.1'
    }

## Check the System Version 检查版本

在使用 Android5.0 代码的时候使用下面的代码做适配：

    // Check if we're running on Android 5.0 or higher
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        // Call some material design APIs here
    } else {
        // Implement this feature without material design
    }

# Selecting Colors with the Palette API

## Set up the library

    android {
      compileSdkVersion 24
      ...
    }

    dependencies {
      ...
      compile 'com.android.support:palette-v7:24.2.1'
    }

## Create a palette

Palette 对象有获取 image 的主颜色的功能。

### Generate a Palette instance 创建 Palette 对象

    // Generate palette synchronously and return it
    public Palette createPaletteSync(Bitmap bitmap) {
      Palette p = Palette.from(bitmap).generate();
      return p;
    }

    // Generate palette asynchronously and use it on a different
    // thread using onGenerated()
    public void createPaletteAsync(Bitmap bitmap) {
      Palette.from(bitmap).generate(new PaletteAsyncListener() {
        public void onGenerated(Palette p) {
          // Use generated instance
        }
      });
    }

### Customize your palette 自定义 palette

Palette.Builder 允许自定义 获取颜色的数量，获取颜色的区域，允许获取的颜色值。

- addFilter()

    添加获取的颜色过滤，Pass in your own Palette.Filter and modify its isAllowed() method to determine which colors are filtered from the palette.
- maximumColorCount()

    设置获取颜色的数量值，默认16
- setRegion()

    定义获取颜色的区域
- addTarget()

    自己定义获取的颜色值

## Extract color profiles 提取颜色参数

可提取的颜色类型：

- Light Vibrant
- Vibrant
- Dark Vibrant
- Light Muted
- Muted
- Dark Muted

`Palette’s get<Profile>Color()`

### Use swatches to create color schemes 使用色板定义颜色策略

Palette 对象也可以使用 Palette.Swatch 生成。

通过调用色板的获取颜色方法：getBodyTextColor() and getTitleTextColor()，可以获取到合适的颜色。

Palette 对象的 `get<Profile>Swatch()` 方法返回色板。

    // Return a palette's vibrant swatch after checking that it exists
    private Palette.Swatch checkVibrantSwatch(Palette p) {
      Palette.Swatch vibrant = p.getVibrantSwatch();
      if (vibrant != null) {
        return vibrant;
      }
      // Throw error
    }

usage：

    // Set the background and text colors of a toolbar given a
    // bitmap image to match
    public void setToolbarColor(Bitmap bitmap) {
      // Generate the palette and get the vibrant swatch
      // See the createPaletteSync() and checkVibrantSwatch() methods
      // from the code snippets above
      Palette p = createPaletteSync(bitmap);
      Palette.Swatch vibrantSwatch = checkVibrantSwatch(p);

      // Set the toolbar background and text colors
      Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
      toolbar.setBackgroundColor(vibrantSwatch.getRgb());
      toolbar.setTitleTextColor(vibrantSwatch.getTitleTextColor());
    }
