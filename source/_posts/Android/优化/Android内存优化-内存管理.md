---
title: Android内存优化 -- 内存管理 onTrimMemory
date: 2017-12-21 21:03:14
tags: [Android优化]
categories: android

---

原文地址: https://mp.weixin.qq.com/s?__biz=MzIxNjc0ODExMA==&amp;mid=2247484311&amp;idx=1&amp;sn=1fe0416bed4137dd45c6e9c153bb14f4&amp;chksm=97851ab6a0f293a0cde28ff6d1091b2232e1758e9845a05549d01c62f412def742985d642630&amp;scene=21#wechat_redirect

# 前言

LowMemoryKiller 策略：App 退出到后台之后不占用 cpu 资源但是任然会占用内存空间。当系统内存不足的时候，会按照优先级清理进程。

App 将内存使用降低可以提高优先级，实现不被系统回收的功能。

# onTrimMemory

## 作用

通过 onTrimMemory 实现对 App 内存的简单管理。

onTrimMemory：Android 4.0 之后提供的 API，作用是提醒开发者系统内存不足了。


```java
public void onTrimMemory(int level)
```

<!--more-->

## onTrimmemory 参数的意义

- TRIM_MEMORY_UI_HIDDEN：App 的所有 UI 界面被隐藏，最常见的就是 App 被 home 键或者 back 键，置换到后台了。
- TRIM_MEMORY_RUNNING_MODERATE：表示 App 正常运行，并且不会被杀掉，但是目前手机内存已经有点低了，系统可能会根据 LRU List 来开始杀进程。
- TRIM_MEMORY_RUNNING_LOW：表示 App正常运行，并且不会被杀掉。但是目前手机内存已经非常低了。
- TRIM_MEMORY_RUNNING_CRITICAL：表示 App 正在正常运行，但是系统已经开始根据 LRU List 的缓存规则杀掉了一部分缓存的进程。这个时候应该尽可能的释放掉不需要的内存资源，否者系统可能会继续杀掉其他缓存中的进程。
- TRIM_MEMORY_BACKGROUND：表示 App 退出到后台，并且已经处于 LRU List 比较靠后的位置，暂时前面还有一些其他的 App 进程，暂时不用担心被杀掉
- TRIM_MENORY_MODERATE：表示 App 退出到后台，并且已经处于 LRU List 中间的位置，如果手机内存仍然不够的话，还是有被杀掉的风险的。
- TRIM_MEMORY_COMPLETE：表示 App 退出到后台，并且已经处于 LRU List 比较考靠前的位置，并且手机内存已经极低，随时都有可能被系统杀掉。


level 分三类：

1. UI 在后台
2. App 在前台运行
3. App 在后台，在 Cached 状态下

## 哪些组件可以监听 onTrimMemory

- Application
- Activity
- Fragment
- Service
- ContentProvider

通常会在 Application 中监听

## 自定义 noTrimMemory 监听

```java
    public void registerComponentCallbacks() {
        synchronized (mTrimMemoryCallback) {
            this.registerComponentCallbacks(mTrimMemoryCallback);
        }
    }

    public void unregisterComponentCallbacks() {
        synchronized (mTrimMemoryCallback) {
            this.unregisterComponentCallbacks(mTrimMemoryCallback);
        }
    }

    public ComponentCallbacks2 mTrimMemoryCallback = new ComponentCallbacks2(){

        @Override
        public void onConfigurationChanged(Configuration newConfig) {

        }

        @Override
        public void onLowMemory() {

        }

        @Override
        public void onTrimMemory(int level) {
            
        }
    };
```


# onTrimMemory 的一些思考

通常需要释放的资源：图片，文件

通过这个实现 应用退到后台 的事件捕获


