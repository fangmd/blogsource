---
title: LiveData
date: 2018-11-12 12:18:12
tags: [Android 架构]
category: android

---

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