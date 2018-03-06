---
title: Activity 跳转动画
date: 2018-02-01 09:38:14
tags: [animaion]
categories: android

---


# Content Transition 内容变换

## 使用步骤

- 在 Activity style 中添加

```java
<item name="android:windowActivityTransitions">true</item>
```

- 设置相应的A离开/B进入/B离开/A重新进入动画。

```java
//A 不设置默认为null
getWindow().setExitTransition(transition);
//B 不设置默认为Fade
getWindow().setEnterTransition(transition);
//B 不设置默认为EnterTransition
getWindow().setReturnTransition(transition);
//A 不设置默认为ExitTransition
getWindow().setReenterTransition(transition);
```

也可以在主题中设置

```java
<item name="android:windowEnterTransition">@transition/slide_and_fade</item>
<item name="android:windowReturnTransition">@transition/return_slide</item>
```


- 跳转界面

```java
Bundle bundle=ActivityOptionsCompat.makeSceneTransitionAnimation(activity).toBundle;
startActivity(intent,bundle)
```

# Shared Element Transition
>Shared Element Transition 通常和 Content Transition 一起使用


## 使用步骤

- 设置需要被共享的元素

```
xml 中设置，或者在代码中设置

transitionName
```


- 跳转界面

```java
Bundle bundle=ActivityOptionsCompat.makeSceneTransitionAnimation(activity).toBundle;
startActivity(intent,bundle)
```



