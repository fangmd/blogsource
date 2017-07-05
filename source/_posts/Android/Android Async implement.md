---
title: Android Async implement
date: 2016-05-10 14:18:12
tags: async
category: android

---

# Android 异步

## AsyncTask 实现异步

    public abstract class AsyncTask<Params, Progress, Result>

### 几个常用的基本回调方法

- `protected void onPreExecute()`
- `protected abstract Result doInBackground(Params... params);`
- `protected void onProgressUpdate(Progress... values)`
- `protected void onPostExecute(Result result)`

### 方法详细介绍：

#### 0 `protected void onPreExecute()`
执行线程：UI线程

执行时间：主线程中调用AsyncTask子类实例的`execute()`方法后

作用：可以用于写一些异步任务开始执行的提示代码

<!--more-->

#### 1 `protected abstract Result doInBackground(Params... params);`
执行线程：子线程

执行时间：在`onPreExecute()`方法后面执行

作用:需要进行异步的代码在这里执行，耗时操作在这里执行

#### 2 `protected void onPostExecute(Result result)`
执行线程：UI线程

执行时间：当子线程中的耗时操作执行完毕后执行

作用：耗时操作的结果放回，可以通过接口回调的方式处理结果

#### 3 `protected void onProgressUpdate(Progress... values)`
执行线程：UI线程

执行时间：AsyncTask子类实例调用`publishProgress`后调用

作用：反馈异步任务执行的进度

### 三个参数
为了调用关系明确及安全，AsynTask类在继承时要传入3个泛型。

- 第一个泛型对应execute（）向doInBackground（）的传递类型。
- 第二个泛型对应doInBackground()的返回类型和传递给onPostExecute()的类型。
- 第三个泛型对应publishProgress()向progressUpdate（）传递的类型。

传递的数据都是对应类型的数组，数组都是可变长的哦。可以根据具体情况使用。

## Handler+Thread 实现异步
适合一些简单的异步操作

### Thread实现方式
- 匿名内部类：好处是可以直接过去到UI线程的handler对象
- Runable实现类单独写个类文件

## RxJava



