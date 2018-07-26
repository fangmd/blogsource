---
title: Android 多线程小结
date: 2017-12-27 12:18:12
tags: [Android]
category: android

---

# Android 中不要自己创建线程，应该使用线程池

工具类

```
int NUMBER_OF_CORES = Runtime.getRuntime().availableProcessors();
int KEEP_ALIVE_TIME = 1;
TimeUnit KEEP_ALIVE_TIME_UNIT = TimeUnit.SECONDS;
BlockingQueue<Runnable> taskQueue = new LinkedBlockingQueue<Runnable>();
ExecutorService executorService = new ThreadPoolExecutor(NUMBER_OF_CORES,
NUMBER_OF_CORES*2, KEEP_ALIVE_TIME, KEEP_ALIVE_TIME_UNIT, taskQueue,
new BackgroundThreadFactory(), new DefaultRejectedExecutionHandler());
//执行任务
executorService.execute(new Runnnable() {
 ...
});
```

# 实现异步的方式

## Java Thread

```java
// 1. 实现Runnable接口
new Thread(new Runnable() {
    @Override
    public void run() {
        // code

    }
}).start();

// 2. 直接继承Thread
public class MyThread extends Thread{
    @Override
    public void run() {
        super.run();
    }
}
```

>缺点是无法获取执行结果

## Future + ExecutorService

>在 Android 中使用的时候要注意在子线程中执行，因为在主线程中直接调用结果获取的方法会阻塞线程。

Future(FutureTask) 作用：

1. 判断任务是否完成；
2. 能够中断任务；
3. 能够获取任务执行结果。

```java
mExecutorService = Executors.newCachedThread
mStringFutureTask = new FutureTask<String>(new CString>() {
    @Override
    public String call() throws Exception {
        Thread.sleep(8000);
        return "result";
    }
});
mExecutorService.execute(mStringFutureTask);
try {
    String s = mStringFutureTask.get();
    Log.d(TAG, "onCreate: 
    // close executorService
    mExecutorService.shutdown();
} catch (InterruptedException e) {
    e.printStackTrace();
} catch (ExecutionException e) {
    e.printStackTrace();
}
```

优点：

1. 能够获取任务结果
2. 在多任务的情况下方便管理


## AsyncTask

>但凡和线程池/线程/同步/锁等有关的实践问题，尽量不要用 Android 的解决方案，如 AsyncTask，而要用 Java 中已经历经考验的实践方案

是适合当个任务，在多个异步任务并行的情况下不方便管理

## IntentService



# ExecutorService 管理异步任务


# 一个例子 使用多线程计算 1+2...+100

```java
private void startCalculate() {
    sum += 100;
    for (int i = 0; i < 10; i++) {
        final int finalI = = 0；
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
              
                synchronized (MainActivity.this) {
                    int from = (100 / 10) * finalI;
                    int to = (100 / 10) * (finalI + 1);
                    for (int j = from; j < to; j++) {
                        sum += j;
                    }
                }
                result(Thread.currentThread().getNamesum);
            }
        }, "Thread" + i);
        thread.start();
    }
}

volatile long sum = 0;

private void result(String name, long sum) {
    Log.d(TAG, name + "result: " + sum);
}
```

- result 方法作为结果回调，可以在 result 中记录已经结束的线程，同时判断是不是所有线程都执行结束了
- error 可以增加一个 error 方法回调，记录哪个线程执行的任务失败了




参考：

- [https://www.jianshu.com/p/2b634a7c49ec](https://www.jianshu.com/p/2b634a7c49ec)
- [http://www.cnblogs.com/dolphin0520/p/3949310.html](http://www.cnblogs.com/dolphin0520/p/3949310.html)
- [https://www.jianshu.com/p/253cff3d895a](https://www.jianshu.com/p/253cff3d895a)