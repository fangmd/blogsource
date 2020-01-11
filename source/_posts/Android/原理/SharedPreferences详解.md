---
title: SharedPreferences 详解
date: 2019-09-04 12:18:12
tags: 源码
category: android

---


参考：[https://juejin.im/post/5d669a27518825271f67d5d4](https://juejin.im/post/5d669a27518825271f67d5d4)


# 获取 SharedPreference 对象

## 方式一 PreferenceManager

```
SharedPreferences defaultSharedPreferences = PreferenceManager.getDefaultSharedPreferences(context);
```

## 方式二 ContextImpl getSharedPreferences

```
sSharedPreferences = context.getSharedPreferences(APP_SP_NAME, Context.MODE_PRIVATE);
```

## SharedPreference 模式

- MODE_WORLD_READABLE (Android N 7.0 后不支持，会直接抛出异常)
- MODE_WORLD_WRITEABLE (Android N 7.0 后不支持，会直接抛出异常)
- MODE_MULTI_PROCESS (并不能保证线程安全)
- MODE_PRIVATE (常用)

## SharedPreferences 对象加载过程

1. 判断 SharedPreferences 是否已经加载进内存
2. 判断是否存在遗留的备份文件，如果存在，重命名为 sp 文件
3. 读取 sp 文件，并存入内存
4. 更新文件信息
5. 释放锁，唤醒处于等待状态的线程


其他:

1. 创建对象并马上调用 getXXX 的时候，如果 sp 文件数据量比较大，getXXX 会阻塞等待文件读取。

# getXXX

```java
> SharedPreferencesImpl.java

@Override
public int getInt(String key, int defValue) {
    synchronized (mLock) {
        awaitLoadedLocked(); // sp 文件尚未加载完成时，会阻塞在这里，见 2.1
        Integer v = (Integer)mMap.get(key); // 加载完成后直接从内存中读取
        return v != null ? v : defValue;
    }
}
```

SP 文件加载完成后，所有的 getXXX 操作都是从内存中读取的。

>SP数据都加载到内存中，也就导致了 SP 不适合存储大数据量的限制

# putXXX

```java
val editor = PreferenceManager.getDefaultSharedPreferences(this).edit()
editor.putInt("key",1)
editor.commit().editor.apply()
```

> 在封装工具类的时候，editor 可以重复使用

## commit

>同步保存，有返回值

1. 首先同步 mModified 到内存中 , commitToMemory()
2. 然后同步内存数据到 sp 文件中 ，enqueueDiskWrite()
3. 等待写入操作完成，并通知监听者

## apply

>异步保存，无返回值

# 问题

1. 不支持跨进程
2. 初始化可能慢
3. SP 数据都会加载到内存中
4. 无论是 commit() 还是 apply() ，针对任何修改都是全量写入。建议针对高频修改的配置项存在子啊单独的 sp 文件。


