---
title: LiveData
date: 2018-11-12 12:18:12
tags: [Android 架构]
category: android

---

# 2018-11-15 LiveData 源码阅读

# 不能白看源码，面试如何回答

> 简单的说下 LiveData 原理？

本质上是一个观察者模式，Activity 或 Fragment 中实现观察者，LiveData 是被观察者，LiveData 中存储的数据变更的时候触发事件。

> 说下 LiveData 的优缺点？

优点：

1. 响应式编程

由于观察者模式的特性，实现了响应式编程，LiveData 数据变化后自动触发 UI 更新，

2. 解耦, 对 MVP 模式的补充

没有对比就没有伤害：传统的 MVP 模式在使用的时候，Presenter 中需要传入一个 接口 作为与 View 交互的桥梁，而 LiveData 刚好可以替代了这个 接口。这样 Presenter 中就不需要持有 View(通常是View实现接口传到Presenter中直接使用) 对象，解耦更加彻底

>应该还不算是 MVVM, 个人理解使用了 DataBinding 才算 MVVM

3. 实现了生命周期的监听， 避免内存泄露

缺点：

暂时没想到

> LiveData 如何实现生命周期的监听？

这个有点复杂了，很难几句话说清楚。

白话版本：Activity, Fragment 中有一个成员变量，这个对象在 Activity 和 Fragment 的生命周期函数中会执行一个类似 `lifecycleChange(event)` 的函数并把当前生命周期事件作为参数传入。然后把生命周期 Event 传递给 LiveData。 最后 LiveData 根据生命周期事件判断是否要取消观察者模式的订阅。

专业版本：Activity, Fragment 中有一个成员变量 `mLifecycleRegistry`，这个对象在 Activity 和 Fragment 的生命周期函数中会执行 `mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.XXXX);` ，然后把生命周期 Event 传递给 LifecycleObserver。 最后 LifecycleObserver 根据生命周期事件判断是否要取消观察者模式的订阅。


> Activity 或者 Fragment 哪些生命周期状态下可以接收事件消息？

`onStart, onResume, onPause`

> 不能接收数据的生命周期时(onStop)，LiveData 中数据变化会不会生效?

可以生效，在 Activity 回到 `onResume` 的时候会显示 LiveData 中最新的数据。

> 知道setValue 和 postValue 的区别吗？

`setValue` 必须在主线程中执行, `postValue` 可以在子线程中执行。

> 如何实现线程切换的？

在内部创建了 `MainHandler` ，构建一个 `Runnable` 给 `MainHandler` 执行，实现线程切换。

# LiveData 源码解析

>LiveData是一个数据持有者，其本身实现了观察者模式，支持数据监控（被观察），并且可以感知组件的生命周期。

## 使用

`LiveData` 是一个抽象类，通常在使用 `LiveData` 的时候我们是使用其子类 `MutableLiveData`。

`XXViewModel.class`

```
// 创建 LiveData
private MutableLiveData<Object> mDataMutableLiveData = new MutableLiveData<>();

// LiveData 改变数据, 触发观察者
mDataMutableLiveData.postValue(object);
// setValue 在主线程中执行
// postValue 可以在子线程中执行

```

`XXActivity`:

```
//获取 ViewModel 对象
mViewModel = ViewModelProviders.of(this).get(XXViewModel.class);

//设置观察者
mViewModel.getDataMutableLiveData().observe(this, this::dataChange);

// LiveData 中数据改变的时候处罚
private void dataChange(Object s) {
    // data change
}
```

## 基本概念

LiveData 使用了观察者模式。

1. 被观察者：`LiveData`
2. 观察者，需要实现：`LifecycleOwner` 接口，`Activity, Fragment` 都实现了 `LifecycleOwner` 接口，所以在使用的时候我们通常使用 `Activity, Fragment` 作为观察者。

观察者唯一的作用是提供：`Lifecycle` 对象。`Lifecycle` 是抽象类，实际返回的是 `LifecycleRegistry` 对象。

`LifecycleOwner.class`:

```
public interface LifecycleOwner {
    @NonNull
    Lifecycle getLifecycle();
}
```

Activity 和 Fragment 中都有成员变量 `mLifecycleRegistry`，并在 `getLifecycle` 函数中返回该对象。

```
private LifecycleRegistry mLifecycleRegistry = new LifecycleRegistry(this);

@Override
public Lifecycle getLifecycle() {
    return mLifecycleRegistry;
}
```


## 被观察者添加观察者过程

调用 LiveData#observer 函数

```
public void observe(@NonNull LifecycleOwner owner, @NonNull Observer<T> observer)
```

`android.arch.lifecycle.Observer` 是一个接口，里面只有一个函数 `onChanged` 作为被观察者数据改变后触发的回调函数。

添加观察者过程实际上是把 `Observer` 传递给 `LiveData` 对象 和 `LifecycleRegistry` 对象。

LiveData 中存储 observer：

```
private SafeIterableMap<Observer<T>, ObserverWrapper> mObservers = new SafeIterableMap<>();

Map: observer: new LifecycleBoundObserver(owner, observer)
```

LifecycleRegistry 中存储 observer:

```
private FastSafeIterableMap<LifecycleObserver, ObserverWithState> mObserverMap = new FastSafeIterableMap<>();

Map: LifecycleBoundObserver(owner, observer): ObserverWithState(observer, initialState)
```

`LifecycleBoundObserver` 对象作为生命周期状态变化后调用函数存在

```
class LifecycleBoundObserver extends ObserverWrapper implements GenericLifecycleObserver

生命周期变化后触发：
public void onStateChanged(LifecycleOwner source, Lifecycle.Event event)
```

到此关键对象：

1. LiveData: 作为数据入口，维护观察者和被观察者的关系
2. Observer：数据改变后最终触发的函数，回调接口，调用端实现
3. LifecycleOwner: Activity, Fragment 提供 Lifecycle
4. Lifecycle, LifecycleRegistry
5. LifecycleObserver，GenericLifecycleObserver, LifecycleBoundObserver: 封装了 Observer, LifecycleOwner 对象

## Activity 和 Fragment 的生命周期是如何和 LiveData 产生关联的？

LiveData 和 生命周期的关系基本操作：

```
生命周期正常的时候， addObserver 注册观察者；生命周期结束的时候 removeObserver 取消观察者
```

上面添加订阅和取消订阅都是内部自动执行的，不需要用户去手动触发。

上面提到 Activity，Fragment 都实现了 `LifecycleOwner` 接口，成员变量中有 `mLifecycleRegistry` 对象。

在 Activty, Fragment 生命周期函数中调用下面函数将生命周期事件传递出来：

```
mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.XXXX);
```

`mLifecycleRegistry` 触发生命周期改变函数后，调用下面函数(中间省略了部分步骤)

```
mLifecycleObserver.onStateChanged(owner, event);

LifecycleBoundObserver 就是这里的 mLifecycleObserver
//根据事件判断是否要解除订阅
```

## setValue 与 postValue

`setValue` 必须在主线程中执行,`postValue` 可以在子线程中执行，

**如何实现的线程切换？**

```
ArchTaskExecutor.getInstance().postToMainThread(mPostValueRunnable);
```

>ArchTaskExecutor 使用了代理模式, 实际函数执行类是 DefaultTaskExecutor

```
TaskExecutor 接口
    |- ArchTaskExecutor 实现
    |- DefaultTaskExecutor 实现
```

Runnable 最终执行位置：

```
    @Override
    public void postToMainThread(Runnable runnable) {
        if (mMainHandler == null) {
            synchronized (mLock) {
                if (mMainHandler == null) {
                    mMainHandler = new Handler(Looper.getMainLooper());
                }
            }
        }
        //noinspection ConstantConditions
        mMainHandler.post(runnable);
    }
```

>线程切换通过 Handler 实现

MainHandler 创建方式：

```
mMainHandler = new Handler(Looper.getMainLooper());
```

判断是否是 MainThread:

```
Looper.getMainLooper().getThread() == Thread.currentThread();
```

# Fun with LiveData (Android Dev Summit '18)

- LiveData 作为 ViewModel 与 Activity 之间沟通的桥梁。
- LiveData 是一个 Observable, Activity 中创建对应的 Observer。
- LiveData is Lifecycle-Aware。

也就是说 Lifecycle(Activity, Fragment) `onStop, onDestory`之后，LiveData 会自动和 Lifecycle 解除绑定，

- LiveData 是一个 Value Holder

给 LiveData 设置一个值，这个值回传递给 Lifecycle(Activity, Fragment).
在监听之外时间设置的 Value 会在监听开始的时候直接获取到。

## LiveData beyond the ViewModel

当我们有 Repository 的时候，Repository 向外暴露 LiveData, 如何将这个 LiveData 和 Lifecycle 绑定？

>if you need a lifecycle in your ViewModel, you probably need Transformation. - Yigit

```
        LiveData<UserEntity> map = Transformations.map(userIdLiveData, new Function<String, UserEntity>() {
            @Override
            public UserEntity apply(String input) {
                return respository,getUser(input);
            }
        });

        LiveData<UserEntity> yLiveData = Transformations.switchMap(userIdLiveData, new Function<String, LiveData<UserEntity>>() {
            @Override
            public LiveData<UserEntity> apply(String input) {
                return respository.getDataForUser(input);
            }
        });
```

## MediatorLiveData 使用

MediatorLiveData 可以监听多个 LiveData。

计算两个 LiveData 中存储的 String 总长度

```
        final MediatorLiveData<Integer> integerMediatorLiveData = new MediatorLiveData<Integer>;
        Observer<String> onChanged = new Observer<String>() {
            @Override
            public void onChanged(String s) {
                String value = userIdLiveData.getValue();
                String value1 = userIdLiveData2.getValue();
                integerMediatorLiveData.setValue(value.length() + value1.length());
            }
        };
        integerMediatorLiveData.addSource(userIdLiveData, onChanged);
        integerMediatorLiveData.addSource(userIdLiveData2, onChanged);
```







[Fun with LiveData (Android Dev Summit '18)](https://www.youtube.com/watch?v=2rO4r-JOQtA&list=PLWz5rJ2EKKc8WFYCR9esqGGY0vOZm2l6e&index=3)