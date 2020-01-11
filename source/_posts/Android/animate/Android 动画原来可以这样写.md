---
title: Android 动画原来可以这样写
date: 2016-10-30 13:18:12
tags: [Android, Animation, Transition]
category: Android

---


# 关键类

- android.transition.TransitionManager
- android.transition.Transition 抽象类
    - TransitionSet
        - AutoTransition
    - ChangeBounds
    - Visibility 抽象类
        - Fade
        - Explode （ design 包中无适配）
        - Slide （ design 包中无适配）
    - TextScale
    - ChangeClipBounds （ design 包中无适配）
    - ChangeImageTransform （ design 包中无适配）
    - ChangeScroll （ design 包中无适配）
    - ChangeTransform （ design 包中无适配）

<!--more-->
## 介绍

Visibility 抽象类的子类：Fade， Explode， Slide 动画作用于 View 的 Visibility 属性改变的时候。

# 适配
对应的适配包在design`com.android.support:design:x.x.x`包中

    compile 'com.android.support:design:25.0.0'

**更好的适配方案：** (使用的时候注意导包 `com.transitionseverywhere.xxx`)

    compile "com.andkulikov:transitionseverywhere:1.6.9"

<!--more-->

# 上代码
> 下面的例子 使用适配包 compile "com.andkulikov:transitionseverywhere:1.6.9" 测试系统 Android4.1

## 默认效果

### 布局关键代码：

    <LinearLayout
        android:id="@+id/ll_container_one"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="vertical">

        <Button
            android:id="@+id/btn_one"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Fad"/>

        <TextView
            android:id="@+id/tv_one"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:text="Transitions are awesome!"
            android:visibility="gone"/>

    </LinearLayout>

### `Activity`中代码：

    final LinearLayout transitionsContainer = (LinearLayout) findViewById(R.id.ll_container_one);
    final TextView text = (TextView) findViewById(R.id.tv_one);
    final Button button = (Button) findViewById(R.id.btn_one);

    button.setOnClickListener(v -> {
        TransitionManager.beginDelayedTransition(transitionsContainer);

        if (text.getVisibility() == View.VISIBLE) {
            text.setVisibility(View.GONE);
        } else {
            text.setVisibility(View.VISIBLE);
        }
    });

### 最终效果：

![默认效果](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transitin_default.gif)

### 代码解释

TransitionManager 中的 beginDelayedTransition 方法：

    public static void beginDelayedTransition(final ViewGroup sceneRoot){...}
    public static void beginDelayedTransition(final ViewGroup sceneRoot, Transition transition) {...}

1. 方法一：效果如上面的默认效果
2. 方法二：参数用于定制动画效果， 参数为 Transition 的子类，常用的有：Fade, ChangeBounds, Slide,

**动画其他参数设置：** 设置动画时间，设置动画加速度，设置动画延时

    transition.setDuration(300);
    transition.setInterpolator(new FastOutSlowInInterpolator());
    transition.setStartDelay(200);

## Fade 淡出淡入

### 布局如上 更改`Activity`中代码：

    final LinearLayout transitionsContainer = (LinearLayout) findViewById(R.id.ll_container_one);
    final TextView text = (TextView) findViewById(R.id.tv_one);
    final Button button = (Button) findViewById(R.id.btn_one);

    button.setOnClickListener(v -> {
        Fade fade = new Fade();
        TransitionManager.beginDelayedTransition(transitionsContainer， fade);

        if (text.getVisibility() == View.VISIBLE) {
            text.setVisibility(View.GONE);
        } else {
            text.setVisibility(View.VISIBLE);
        }
    });

### 最终效果

![Fade效果](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transitin_fade.gif)

## Slide 移动

### 布局如上 更改`Activity`中代码：

    final LinearLayout transitionsContainer = (LinearLayout) findViewById(R.id.ll_container_one);
    final TextView text = (TextView) findViewById(R.id.tv_one);
    final Button button = (Button) findViewById(R.id.btn_one);

    button.setOnClickListener(v -> {
        // Slide slide = new Slide();
        Slide slide = new Slide(Gravity.RIGHT);
        TransitionManager.beginDelayedTransition(transitionsContainer, slide);

        if (text.getVisibility() == View.VISIBLE) {
            text.setVisibility(View.GONE);
        } else {
            text.setVisibility(View.VISIBLE);
        }
    });

### 最终效果

![Slide](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transitin_slide.gif)

### Slide 构造方法

    /**
     * Constructor using the default {@link Gravity#BOTTOM}
     * slide edge direction.
     */
    public Slide() {
        setSlideEdge(Gravity.BOTTOM);
    }

设置滑出方向：

    /**
     * Constructor using the provided slide edge direction.
     */
    public Slide(@GravityFlag int slideEdge) {
        setSlideEdge(slideEdge);
    }

## Scale 缩放

### 布局如上 更改`Activity`中代码：

    final LinearLayout transitionsContainer = (LinearLayout) findViewById(R.id.ll_container_one);
    final TextView text = (TextView) findViewById(R.id.tv_one);
    final Button button = (Button) findViewById(R.id.btn_one);

    button.setOnClickListener(v -> {
        // Scale scale = new Scale();
        Scale scale = new Scale(0.7f);
        TransitionManager.beginDelayedTransition(transitionsContainer, scale);

        if (text.getVisibility() == View.VISIBLE) {
            text.setVisibility(View.GONE);
        } else {
            text.setVisibility(View.VISIBLE);
        }
    });

### 最终效果

![Scale](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transitin_scale.gif)

### Scale 构造方法

    public Scale() {
    }

    /**
     * @param disappearedScale Value of scale on start of appearing or in finish of disappearing.
     *                         Default value is 0. Can be useful for mixing some Visibility
     *                         transitions, for example Scale and Fade
     */
    public Scale(float disappearedScale) {
        setDisappearedScale(disappearedScale);
    }

参数设置了缩放起始值或者最终值。

## TransitionSet 动画集合

    final LinearLayout transitionsContainer = (LinearLayout) findViewById(R.id.ll_container_one);
    final TextView text = (TextView) findViewById(R.id.tv_one);
    final Button button = (Button) findViewById(R.id.btn_one);

    button.setOnClickListener(v -> {

        TransitionSet set = new TransitionSet()
                .addTransition(new Scale(0.7f))
                .addTransition(new Fade())
                .setInterpolator(visible ? new LinearOutSlowInInterpolator() :
                        new FastOutLinearInInterpolator());

        TransitionManager.beginDelayedTransition(transitionsContainer, set);

        if (text.getVisibility() == View.VISIBLE) {
            text.setVisibility(View.GONE);
        } else {
            text.setVisibility(View.VISIBLE);
        }
    });

### 最终效果

![TransitionSet](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_set.gif)


## Recolor 颜色渐变

空间的背景色或者文字颜色改变的动画

### 布局

    <LinearLayout
        android:id="@+id/ll_container_recolor"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="vertical"
        android:visibility="visible">


        <Button
            android:id="@+id/btn_recolor"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Recolor"/>

        <Button
            android:id="@+id/btn_normal"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:text="Normal"
            android:visibility="visible"/>

    </LinearLayout>

### `Activity` 代码

    final ViewGroup transitionsContainerRecolor = (ViewGroup) findViewById(R.id.ll_container_recolor);
    final Button btnRecolor = (Button) findViewById(R.id.btn_recolor);
    final Button btnNormal = (Button) findViewById(R.id.btn_normal);

    int green = getResources().getColor(R.color.green);
    int white = getResources().getColor(R.color.white);
    int grey = getResources().getColor(R.color.grey);

    btnRecolor.setOnClickListener(v -> {
        TransitionManager.beginDelayedTransition(transitionsContainerRecolor, new Recolor());
    //            btnRecolor.setBackgroundColor(visible ? green : white); // 无动画效果
        btnRecolor.setTextColor(visible ? white : grey);
    //            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
    //                btnRecolor.setBackground(new ColorDrawable(visible ? green : white));
    //            } else {
        btnRecolor.setBackgroundDrawable(new ColorDrawable(visible ? green : white));
    //            }
        visible = !visible;
    });

    btnNormal.setOnClickListener(v -> {
        btnNormal.setBackgroundColor(visible ? green : white);
        btnNormal.setTextColor(visible ? white : green);
        visible = !visible;
    });

**注意：** `btnRecolor.setBackgroundColor(visible ? green : white); // 无动画效果` 通过 `setBackgroundColor` 背景色时没有动画效果，可以使用 `setBackground`, `setBackgroundDrawable`

### 最终效果

![ReColor](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_recolor.gif)

## Rotate 旋转

### 布局

    <LinearLayout
        android:id="@+id/ll_container_rotate"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="vertical"
        android:visibility="visible">


        <Button
            android:id="@+id/btn_rotate"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Rotate"/>

        <ImageView
            android:id="@+id/iv_rotate"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:layout_marginTop="10dp"
            android:src="@drawable/ic_clear_black_24dp"
            android:visibility="visible"/>

    </LinearLayout>

### `Activity` 代码

    final ViewGroup transitionsContainerRotate = (ViewGroup) findViewById(R.id.ll_container_rotate);
    final Button btnRotate = (Button) findViewById(R.id.btn_rotate);
    ImageView ivRotate = (ImageView) findViewById(R.id.iv_rotate);

    btnRotate.setOnClickListener(v -> {
        TransitionManager.beginDelayedTransition(transitionsContainerRotate, new Rotate());
        ivRotate.setRotation(isRotated ? 0 : 135);
        isRotated = !isRotated;
    });

### 最终效果

 ![Rotate](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_rotate.gif)


## ChangeText 文字改变时动画

### 布局

    <LinearLayout
        android:id="@+id/ll_container_change_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="vertical"
        android:visibility="visible">

        <Button
            android:id="@+id/btn_change_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="ChangeText"/>

        <TextView
            android:id="@+id/tv_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:text="Transitions are awesome!"
            android:visibility="visible"/>

    </LinearLayout>


### `Activity` 代码

    final LinearLayout transitionsContainerChangeText = (LinearLayout) findViewById(R.id.ll_container_change_text);
    final TextView tvText = (TextView) findViewById(R.id.tv_text);
    final Button btnChangeText = (Button) findViewById(R.id.btn_change_text);
    String secText = " Second Text";
    String firstText = "First Text";

    btnChangeText.setOnClickListener(v -> {
        TransitionManager.beginDelayedTransition(transitionsContainerChangeText,
                new ChangeText().setChangeBehavior(ChangeText.CHANGE_BEHAVIOR_OUT_IN));

        tvText.setText(isFirstText ? secText : firstText);
        isFirstText = !isFirstText;

    });

### 最终效果

![ChangeText](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_change_text.gif)


## TransitionName 制作 打乱动画

使用场景：

1. 需要动态生成 ViewGroup 的子 View ， 并且子 View 内容需要更新时
2. 需要制作随机时

### 布局

**注意：**这里把 Button 移到了 LinearLayout 的外面，原因是一会创建子 View 的时候会先删除所有子 View（Button 也会被删除）。

    <Button
        android:id="@+id/btn_transition_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="打乱"/>


    <LinearLayout
        android:id="@+id/ll_container_transition_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="vertical"
        android:visibility="visible">

### `Activity` 代码

    // TransitionName 做打乱动画
    final LinearLayout transitionsContainerTransitionName = (LinearLayout) findViewById(R.id.ll_container_transition_name);
    final Button btnTransitionName = (Button) findViewById(R.id.btn_transition_name);

    LayoutInflater inflater = LayoutInflater.from(this);
    ArrayList<String> titles = new ArrayList<>();
    for (int i = 0; i < 4; i++) {
        titles.add(String.format(Locale.CHINA, "Item %d", i));
    }
    createViews(inflater, transitionsContainerTransitionName, titles);

    btnTransitionName.setOnClickListener(v -> {
        TransitionManager.beginDelayedTransition(transitionsContainerTransitionName, new ChangeBounds());
        Collections.shuffle(titles);
        createViews(inflater, transitionsContainerTransitionName, titles);
    });

    // 独立的方法
    // In createViews we should provide transition name for every view.
    private static void createViews(LayoutInflater inflater, ViewGroup layout, List<String> titles) {
        layout.removeAllViews();
        for (String title : titles) {
            TextView textView = (TextView) inflater.inflate(R.layout.text_view, layout, false);
            textView.setText(title);
            TransitionManager.setTransitionName(textView, title);
            layout.addView(textView);
        }
    }

### 最终效果

![TransitionName](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_transition_name.gif)


## Explode and Propagation 爆炸和传播

爆炸效果，和移动过渡动画比较相似，不过子 View 的移动方向是由其所在的位置决定的。子 View 的移动方向需要通过计算得到（通过 `setEpicenterCallback` 方法）

### 关键代码

这个例子使用 RecyclerView 和 GridLayoutManager 做基本布局，点击里面的 item 让其消失。

    public void onClick(View clickedView) {
        // save rect of view in screen coordinates
        final Rect viewRect = new Rect();
        clickedView.getGlobalVisibleRect(viewRect);

        // create Explode transition with epicenter
        Transition explode = new Explode()
            .setEpicenterCallback(new Transition.EpicenterCallback() {
                @Override
                public Rect onGetEpicenter(Transition transition) {
                    return viewRect;
                }
            });
        explode.setDuration(1000);
        TransitionManager.beginDelayedTransition(recyclerView, explode);

        // remove all views from Recycler View
        recyclerView.setAdapter(null);
    }

### 最终效果

![Exlode](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_explode.gif)




## ChangeImageTransform

## Path (Curved) motion 路径过渡动画

所有的过渡动画都需要两个值：起始值和结束值

比如：通过 ChangeBounds 来改变 view 的位置，通过 setPathMotion 来提供路径

### 布局代码

    <FrameLayout
        android:id="@+id/fl_container_path_motion"
        android:layout_width="match_parent"
        android:layout_height="match_parent">


        <Button
            android:id="@+id/btn_path_motion"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Path Motion"/>


    </FrameLayout>

### `Activity` 代码

    // path motion 路径过渡动画
    final FrameLayout transitionsContainerPathMotion = (FrameLayout) findViewById(R.id.fl_container_path_motion);
    Button btnPathMotion = (Button) findViewById(R.id.btn_path_motion);

    btnPathMotion.setOnClickListener(v -> {

        TransitionManager.beginDelayedTransition(transitionsContainerPathMotion,
                new ChangeBounds().setPathMotion(new ArcMotion()).setDuration(500));

        FrameLayout.LayoutParams params = (FrameLayout.LayoutParams) btnPathMotion.getLayoutParams();
        params.gravity = isReturnAnimation ? (Gravity.LEFT | Gravity.TOP) :
                (Gravity.BOTTOM | Gravity.RIGHT);
        btnPathMotion.setLayoutParams(params);
        isReturnAnimation = !isReturnAnimation;

    });

这里通过 LayoutParams 来控制 Button 在其父控件中的位置。


### 最终效果

![Path Motion](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_path_motion.gif)


## Targets 设置动画的目标对象

### 小总结

定义动画：

    TransitionManager.beginDelayedTransition( viewGroup, transition);

默认情况下，这里的 transition 动画会作用于 viewGroup 中的所有子 View

当我们需要在一个 `ViewGroup` 中定义多个动画，作用于不同的子 `View` 该如何做？

### 比如 让一个 TextView 移动， 另一个 TextView 淡出

#### 布局

    <LinearLayout
               android:id="@+id/ll_container_target"
               android:layout_width="wrap_content"
               android:layout_height="wrap_content"
               android:gravity="center"
               android:orientation="vertical"
               android:visibility="visible">

               <Button
                   android:id="@+id/btn_target"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:text="Target"/>

               <TextView
                   android:id="@+id/tv_target_fade"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:layout_marginTop="16dp"
                   android:text="Transitions are awesome fade!"
                   android:visibility="visible"/>

               <TextView
                   android:id="@+id/tv_target_slide"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:layout_marginTop="16dp"
                   android:text="Transitions are awesome slide!"
                   android:visibility="visible"/>

           </LinearLayout>

#### `Activity` 代码


    // Targets 设置动画的目标对象
     final LinearLayout transitionsContainerTarget = (LinearLayout) findViewById(R.id.ll_container_target);
     final Button btnTarget = (Button) findViewById(R.id.btn_target);
     final TextView tvFade = (TextView) findViewById(R.id.tv_target_fade);
     final TextView tvSlide = (TextView) findViewById(R.id.tv_target_slide);


     btnTarget.setOnClickListener(v -> {
         Slide slide = new Slide(Gravity.RIGHT);
         slide.excludeTarget(tvFade, true);

         Fade fade = new Fade();
         fade.excludeTarget(tvSlide, true);

         TransitionSet transitionSet = new TransitionSet()
                 .addTransition(slide)
                 .addTransition(fade);

         TransitionManager.beginDelayedTransition(transitionsContainerTarget, transitionSet);

         if (tvFade.getVisibility() == View.VISIBLE) {
             tvFade.setVisibility(View.GONE);
             tvSlide.setVisibility(View.GONE);
         } else {
             tvFade.setVisibility(View.VISIBLE);
             tvSlide.setVisibility(View.VISIBLE);
         }

     });

#### 最终效果

![Target处理](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/animation_transition_target.gif)

### transition 其他有关 target 方法

Methods to add target:

- addTarget(View target) — view itself
- addTarget(int targetViewId) — id of view
- addTarget(String targetName) — do you remember about method TransitionManager.setTransitionName?
- addTarget(Class targetType) — for example android.widget.TextView.class

To remove target:

- removeTarget(View target)
- removeTarget(int targetId)
- removeTarget(String targetName)
- removeTarget(Class target)

To exclude some views:

- excludeTarget(View target, boolean exclude)
- excludeTarget(int targetId, boolean exclude)
- excludeTarget(Class type, boolean exclude)
- excludeTarget(Class type, boolean exclude)

And for excluding all children of some ViewGroup:

- excludeChildren(View target, boolean exclude)
- excludeChildren(int targetId, boolean exclude)
- excludeChildren(Class type, boolean exclude)


## 使用 xml 创建 Transition

    <?xml version="1.0" encoding="utf-8"?>
    <transitionSet xmlns:app="http://schemas.android.com/apk/res-auto"
                  app:transitionOrdering="together"
                  app:duration="400">
        <changeBounds/>
        <changeImageTransform/>
        <fade
           app:fadingMode="fade_in"
           app:startDelay="200">
            <targets>
                <target app:targetId="@id/transition_title"/>
            </targets>
        </fade>
    </transitionSet>

使用

    TransitionInflater.from(getContext()).inflateTransition(R.anim.my_the_best_transition);


## Activity and Fragment transitions


## Custom Transitions



上面所有代码在：[github](https://github.com/fangmd/Animation/tree/master)

参考：
- [Animate all the things. Transitions in Android](https://medium.com/@andkulikov/animate-all-the-things-transitions-in-android-914af5477d50#.oe9uzkhcq)
- [原作者github](https://github.com/andkulikov/Transitions-Everywhere)
