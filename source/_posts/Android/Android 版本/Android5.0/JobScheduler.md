---
title: JobScheduler 
date: 2016-01-13 12:18:12
tags: [android5.0, 波纹效果]
category: Android

---

Android 5.0 出现了一个新的 API JobScheduler。

功能：定义一些系统在稍后或者指定条件下（如设备充电，连接wifi 时）以异步的方式运行的作业。从而优化电池寿命

应用场景：

1. 应用具有不面向用户并且可以推迟的作业
2. 应用具有在设备充电状态下执行的任务
3. 应用有需要在网络连接或者 Wi-Fi 连接的状态执行的任务	
4. 应用有定时执行的任务


# JobInfo

一个作业使用一个 JobInfo 对象封装。

## JobInfo 创建

构造者模式

使用 `JobInfo.Builder` 创建对象：

```java
public Builder(int jobId, ComponentName jobService)
```

例子：
```java
JobInfo uploadTask = new JobInfo.Builder(mJobId,
                                         mServiceComponent /* JobService component */)
        .setRequiredNetworkCapabilities(JobInfo.NetworkType.UNMETERED)
        .build();
JobScheduler jobScheduler =
        (JobScheduler) context.getSystemService(Context.JOB_SCHEDULER_SERVICE);
jobScheduler.schedule(uploadTask);
```

可以设置作业触发状态：

- `setRequiredNetworkType(int networkType)`: 设置要求的网络，只有接入给定类型的网络才能执行

	```
	NETWORK_TYPE_NONE			默认值，表示与网络状态无关
	NETWORK_TYPE_NOT_ROAMING	表示连接非漫游的网络
	NETWORK_TYPE_UNMETERED		表示无限流量（连接wifi），非计费网络
	NETWORK_TYPE_ANY			表示有网络连接的时候
	```

- `setRequiresCharging (boolean requiresCharging)`	设置是否需要充电器接入。默认false
- `setRequiresDeviceIdle (boolean requiresDeviceIdle)`	设置是否需要设备处于空闲状态。默认false
	
	空闲状态指设置已经有一段时间没有被使用

- `addTriggerContentUri (JobInfo.TriggerContentUri uri)` 利用ContentObserver来监控一个Content Uri
	
	***注意：***为了持续监控 content 的变化，你需要在最近的任务触发后再调度一个新的任务

- `setTriggerContentMaxDelay (long durationMs)` 设置从 content 变化到任务被执行，中间的最大延迟

- `setTriggerContentUpdateDelay (long durationMs)` 设置从content变化到任务被执行中间的延迟。如果在延迟期间content发生了变化，延迟会重新计算
- `.setPersisted(true)` 设置周期执行任务
- `.setPeriodic(500)` 设置周期任务的周期, 通常周期最短时间为 15min

	```java
	    /* Minimum interval for a periodic job, in milliseconds. */
    private static final long MIN_PERIOD_MILLIS = 15 * 60 * 1000L;   // 15 minutes

    /* Minimum flex for a periodic job, in milliseconds. */
    private static final long MIN_FLEX_MILLIS = 5 * 60 * 1000L; // 5 minutes

        /**
     * Query the minimum flex time allowed for periodic scheduled jobs.  Attempting
     * to declare a shorter flex time than this when scheduling such a job will
     * result in this amount as the effective flex time for the job.
     *
     * @return The minimum available flex time for scheduling periodic jobs, in milliseconds.
     */
    public static final long getMinFlexMillis() {
        return MIN_FLEX_MILLIS;
    }
    ```

向 JobService 传值：

```java
setExtras(PersistableBundle extras)
```



### JobInfo.TriggerContentUri

保存了一项任务触发绑定的content uri信息


# JobParameters

当任务被调度，交由应用处理时提供的对象，包含了该任务的信息。

通过 `get` 方法获取 `JobParameters` 内部的数据：

- `PersistableBundle getExtras()`
- `int getJobId()`
- `String getTriggeredContentAuthorities()` 获得触发该任务的content authorities
- `Uri[] getTriggeredContentUris()`
- `boolean isOverrideDeadlineExpired()` 判断该调度是否因为达到deadline了


# JobScheduler

负责调度任务。一般调用schedule(JobInfo)方法将任务加入到调度队列中。

JobScheduler 对象获取：
```java
JobScheduler jobScheduler = (JobScheduler) App.getInstance().getSystemService(Context.JOB_SCHEDULER_SERVICE);
```

- `int schedule(JobInfo)` 将任务加入到调度队列中。将会返回一个结果

	- RESULT_SUCCESS 加入成功
	- RESULT_FAILURE 不合法的参数将会导致失败，有可能是该任务的run-time太短（不是很懂），或者其指定的JobService无法解析
- `cancel(int jobId)` 取消对应ID的任务
- `cancelAll()` 取消由这个包注册的所有任务
- `List< JobInfo > getAllPendingJobs()` 获得这个包注册的正在等待的任务
- `JobInfo getPendingJob(int jobId)` 获得指定的由该包注册的正在等待的任务


# JobService

JobScheduler 的回调入口 

由于需要应用来完成任务的执行，因此需要继承该类，重载其onStartJob(JobParameter)方法，进行任务的执行

重要的方法：

- `void jobFinished (JobParameters params, boolean needsReschedule)` 当完成任务的执行时，调用该方法通知JobManager。该方法可以在任何线程调用

	参数一：从 `onStartJob` 中获取
	参数二：表示是否要重新调度

- `boolean onStartJob (JobParameters params)` 

	JobService 将会在这个回调方法获得可执行的任务，如果该任务不需要额外的执行，可以立即返回false。否则需要在单独的线程中执行（使用Handler），并且返回true，在任务执行完后调用jobFinished()方法进行通知

- `boolean onStopJob (JobParameters params)`

	当你主动通知任务执行完毕（jobFinished）之前，系统可能会要求你停止任务，这时将会调用onStopJob方法 
	当该任务的需求不再满足时将发生这种状况，必须对此做出反应，否则应用可能会出现行为异常。一种立即引起的影响就是系统可能会将你的wakelock释放 
	返回true表示你希望对该任务重新进行调度，同样需要遵守退避策略；返回false表示你希望放弃该任务

# 例子 在手机连接 wifi 的时候下载更新包


## 创建 JobService 子类 DownloadJobService

```java
@RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
public class DownloadJobService extends JobService {

    private Observable<String> mObservable;
    private Disposable mD;

    @Override
    public boolean onStartJob(JobParameters params) {
        LoggerUtils.d(JOBSERVICE, "JobService OnStartJob");


        mObservable = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> e) throws Exception {
                e.onNext("short");
            }
        });

        mObservable.observeOn(Schedulers.io())
                .subscribeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<String>() {

                    @Override
                    public void onSubscribe(Disposable d) {
                        mD = d;
                    }

                    @Override
                    public void onNext(String s) {
                        LoggerUtils.d("receive " + s);
                        jobFinished(params, false); // 主动结束任务
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });

        return false; // return true: 如果有异步任务， false：如果没有异步任务
    }

    /**
     * 任务被动结束的时候调用
     */
    @Override
    public boolean onStopJob(JobParameters params) {
        LoggerUtils.d(JOBSERVICE, "JobService onStopJob");
        if (!mD.isDisposed()) {
            mD.dispose();
        }

        return true; // 返回true表示你希望对该任务重新进行调度，同样需要遵守退避策略；返回false表示你希望放弃该任务
    }

}
```

## 在合适的位置开启任务

```java
 @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
    private void jobSche(){

        JobScheduler jobScheduler = (JobScheduler) App.getInstance().getSystemService(Context.JOB_SCHEDULER_SERVICE);
        ComponentName jobService = new ComponentName(this, DownloadJobService.class);

        int jobId = 123;
        JobInfo jobInfo = new JobInfo.Builder(jobId, jobService)
                .setRequiredNetworkType(NETWORK_TYPE_ANY) // net is available
                .build();

        jobScheduler.schedule(jobInfo);
    }
```


参考：

- [https://developer.android.com/about/versions/android-5.0.html](https://developer.android.com/about/versions/android-5.0.html)
- [http://blog.csdn.net/u013400743/article/details/52246952](http://blog.csdn.net/u013400743/article/details/52246952)