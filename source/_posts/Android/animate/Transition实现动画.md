---
title: Transition 动画
date: 2017-09-25 09:38:14
tags: [Source Code]
categories: android

---

# 关于 transition 

google 关于 transition 的文档地址 [https://developer.android.com/training/transitions/index.html](https://developer.android.com/training/transitions/index.html)

下面是文档的部分渣翻：

## The Transitions Framework

这个框架用于帮助开发者创建动画，帮助用户了解界面的变化过程。


这个框架具有的特性：

1. Group-level animations：可以同时创建多个动画
2. Transition-based animation：基于开始和结束时候 view 的属性创建动画
3. Build-in animations：内置了一些已经实现好的动画效果，比如 渐入，渐出，移动
4. Resource file support：支持使用 xml 文件定义动画
5. Lifecycle callbacks：支持动画过程的回调

![transitions 动画](https://developer.android.com/images/transitions/transitions_diagram.png)

<!--more-->

### Scenes

存储了 View hierarchy 的状态，包含内部的所有 view 和 view 的属性。

The transitions framework 帮助我们从 xml 文件或者 ViewGroup 对象中 创建 Scenes 。

#### 创建一个 Scene

>The transitions framework can run animations between a starting and an ending scene. The starting scene is often determined automatically from the current state of the user interface. For the ending scene, the framework enables you to create a scene from a layout resource file or from a group of views in your code.

##### create a scene from a layout resource

```java
public static Scene getSceneForLayout(ViewGroup sceneRoot, int layoutId, Context context)

//....

Scene.getSceneForLayout()
```

###### 例子

res/layout/activity_main.xml:

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/master_layout">
    <TextView
        android:id="@+id/title"
        ...
        android:text="Title"/>
    <FrameLayout
        android:id="@+id/scene_root">
        <include layout="@layout/a_scene" />
    </FrameLayout>
</LinearLayout>
```

res/layout/a_scene.xml:

```
RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/scene_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <TextView
        android:id="@+id/text_view1
        android:text="Text Line 1" />
    <TextView
        android:id="@+id/text_view2
        android:text="Text Line 2" />
</RelativeLayout>
```

res/layout/another_scene.xml:

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/scene_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <TextView
        android:id="@+id/text_view2
        android:text="Text Line 2" />
    <TextView
        android:id="@+id/text_view1
        android:text="Text Line 1" />
</RelativeLayout>
```


创建 Scenes：

```java
Scene mAScene;
Scene mAnotherScene;

// Create the scene root for the scenes in this app
mSceneRoot = (ViewGroup) findViewById(R.id.scene_root);

// Create the scenes
mAScene = Scene.getSceneForLayout(mSceneRoot, R.layout.a_scene, this);
mAnotherScene =
    Scene.getSceneForLayout(mSceneRoot, R.layout.another_scene, this);

```


##### create a scene in your code


```java
Scene mScene;

// Obtain the scene root element
mSceneRoot = (ViewGroup) mSomeLayoutElement;

// Obtain the view hierarchy to add as a child of
// the scene root when this scene is entered
mViewHierarchy = (ViewGroup) someOtherLayoutElement;

// Create a scene
mScene = new Scene(mSceneRoot, mViewHierarchy);
```

##### Create Scene Actions

>The framework enables you to define custom scene actions that the system >>runs when entering or exiting a scene. In many cases, defining custom scene actions is not necessary, since the framework animates the change between scenes automatically.




### Transitions

动画信息被存储在 Transitin 对象中。

使用 TransitionManager 对象来使用 Transition 对象。


#### Applying a Transition 

Transition 里面包含了动画的信息。执行一个动画，需要提供一个 Transitin 和 一个 ending scene 给 TransitionManager。


##### Create a Transition

###### Create a transition instance from a resource file

1. 创建资源文件夹 `res/transition/`
2. 在这个文件夹中创建 xml

xml 例子:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fade xmlns:android="http://schemas.android.com/apk/res/"
      android:duration="500"/>
```


使用:

```java
Transition mFadeTransition =
        TransitionInflater.from(this).
        inflateTransition(R.transition.fade_transition);

```

###### Create a transition instance in code

```java
Transition mFadeTransition = new Fade();
```


##### Apply a Transition

```java
TransitionManager.go(mEndingScene, mFadeTransition);

TransitionManager.go(mEndingScene);
```

startScene: 不提供的时候，就是当前 View 的状态   

在没有提供 Transition 的时候 TransitionManager 会使用一个默认的 Transition `private static Transition sDefaultTransition = new AutoTransition();`

##### Choose Specific Target Views

默认 Transition 会作用于 ViewGroup 中的所有子 View。但是也可以指定某些 View 可以有动画。

通过 `removeTarget()` 方法设置哪些不执行动画的 View

```java
transition.removeTarget(view)
```

##### Specify Multiple Transitions

```xml
<transitionSet xmlns:android="http://schemas.android.com/apk/res/android"
    android:transitionOrdering="sequential">
    <fade android:fadingMode="fade_out" />
    <changeBounds />
    <fade android:fadingMode="fade_in" />
</transitionSet>
```

##### Apply a Transition Without Scenes

改变 View hierarchies 不是改变用户界面唯一方式，可以通过在一个 view hierarchies 中添加一个 view，删除一个 view 或者改变 view 的属性改变界面。

如果 view hierarchies 没有改变，只是改变 view hierarchies 中子控件的属性，就不需要提供 scene。

这种情况下实现 transition 动画的步骤：

1. `TransitionManager.beginDelayedTransition(mViewGroup);`
2. 改变 子view 的属性


##### Define Transition Lifecycle Callbacks

```java
 Transition transition = TransitionInfthis).inflateTransition(R.transition.temp);
 transition.addListener(new Transition.Transition{
     @Override
     public void onTransitionStart(Transition transition) {
         
 
     @Override
     public void onTransitionEnd(Transition tra
 
     @Override
     public void onTransitionCancel(Transition tra
 
     @Override
     public void onTransitionPause(Transition tra
 
     @Override
     public void onTransitionResume(Transition tra
     }
 })
```


##### Creating Custom Transitions

1. 继承自 Transition

```java
public class CustomTransition extends Transition {

    @Override
    public void captureStartValues(TransitionValues transitionValues) {

    }

    @Override
    public void captureEndValues(TransitionValues transitionValues) {

    }

    @Nullable
    @Override
    public Animator createAnimator(@NonNull ViewGroup sceneRoot, @Nullable TransitionValues startValues, @Nullable TransitionValues endValues) {
        return super.createAnimator(sceneRoot, startValues, endValues);
    }
}
```


###### Capture View Property Values

Transition 动画是通过属性动画实现的。

所以 Transition 动画需要捕获 View 的属性值。


```java
    // Define a key for storing a property value in
    // TransitionValues.values with the syntax
    // package_name:transition_class:property_name to avoid collisions
    private static final String PROPNAME_BACKGROUND =
            "com.nerc.myapplication.transitionAnimate:CustomTransition:background";

    @Override
    public void captureStartValues(TransitionValues transitionValues) {
        captureValues(transitionValues);
    }

    @Override
    public void captureEndValues(TransitionValues transitionValues) {
        captureValues(transitionValues);
    }

    // For the view in transitionValues.view, get the values you
    // want and put them in transitionValues.values
    private void captureValues(TransitionValues transitionValues) {
        // Get a reference to the view
        View view = transitionValues.view;
        // Store its background property in the values map
        transitionValues.values.put(PROPNAME_BACKGROUND, view.getBackground());
    }
```

- PROPNAME_BACKGROUND: 用于定义控件的那些属性需要被记录

###### Create a Custom Animator

定义属性变化过程中的动画效果。

覆盖 `createAnimator()` 方法


###### 一个渐变色例子

```java
public class CustomTransition extends Transition {

    // Define a key for storing a property value in
    // TransitionValues.values with the syntax
    // package_name:transition_class:property_name to avoid collisions
    private static final String PROPNAME_BACKGROUND =
            "com.nerc.myapplication.transitionAnimate:CustomTransition:background";

    public static final Property<ColorDrawable, Integer> COLORDRAWABLE_COLOR  = new IntProperty<ColorDrawable>() {
        @Override
        public void setValue(ColorDrawable object, int value) {
            object.setColor(value);
        }

        @Override
        public Integer get(ColorDrawable object) {
            return object.getColor();
        }

    }.optimize();


    @Override
    public void captureStartValues(TransitionValues transitionValues) {
        captureValues(transitionValues);
    }

    @Override
    public void captureEndValues(TransitionValues transitionValues) {
        captureValues(transitionValues);
    }

    // For the view in transitionValues.view, get the values you
    // want and put them in transitionValues.values
    private void captureValues(TransitionValues transitionValues) {
        // Get a reference to the view
        View view = transitionValues.view;
        // Store its background property in the values map
        transitionValues.values.put(PROPNAME_BACKGROUND, view.getBackground());
    }

    @Nullable
    @Override
    public Animator createAnimator(@NonNull ViewGroup sceneRoot, @Nullable TransitionValues startValues, @Nullable TransitionValues endValues) {
        if (startValues == null || endValues == null) {
            return null;
        }
        final View view = endValues.view;
        Drawable startBackground = (Drawable) startValues.values.get(PROPNAME_BACKGROUND);
        Drawable endBackground = (Drawable) endValues.values.get(PROPNAME_BACKGROUND);
        ObjectAnimator bgAnimator = null;
        if (startBackground instanceof ColorDrawable && endBackground instanceof ColorDrawable) {
            ColorDrawable startColor = (ColorDrawable) startBackground;
            ColorDrawable endColor = (ColorDrawable) endBackground;
            if (startColor.getColor() != endColor.getColor()) {
                final int finalColor = endColor.getColor();
                endColor.setColor(startColor.getColor());
                bgAnimator = ObjectAnimator.ofInt(endColor, COLORDRAWABLE_COLOR, startColor.getColor(), finalColor);
                bgAnimator.setEvaluator(new ArgbEvaluator());
            }
        }

        return bgAnimator;
    }
    
}
```







