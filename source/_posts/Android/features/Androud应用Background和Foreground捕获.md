---
title: Android 应用 Background 和 Foreground 捕获
date: 2018-02-07 09:23:14
tags: features
categories: android

---

实现应用前后台切换的监听

面向接口编程：

```java
public interface BackgroundForegroundDelegate {
    
    void onBackground();
    
    void onForeground();
}
```

<!--more-->

让 Application 实现这个接口。

App.class

```java
public class App extends Application implements BackgroundForegroundDelegate {

    private static App sApp;
    public static App getInstance() {
        return sApp;
    }

    private BackgroundForegroundHandler mBackgroundForegroundHandler;
    private boolean mIsBackground = true;

    @Override
    public void onCreate() {
        super.onCreate();
        sApp = this;
        mBackgroundForegroundHandler = new BackgroundForegroundHandler(this);
        registerLifecycleHandler();
    }

    private void registerLifecycleHandler() {
        registerActivityLifecycleCallbacks();
        registerComponentCallbacks();
    }

    private void unregisterLifecycleHandler() {
        unregisterActivityLifecycleCallbacks();
        unregisterComponentCallbacks();
    }

    public void registerActivityLifecycleCallbacks() {
        synchronized (sApp) {
            registerActivityLifecycleCallbacks(mBackgroundForegroundHandler);
        }
    }

    public void unregisterActivityLifecycleCallbacks() {
        synchronized (sApp) {
            unregisterActivityLifecycleCallbacks(mBackgroundForegroundHandler);
        }
    }

    public void registerComponentCallbacks() {
        synchronized (mBackgroundForegroundHandler) {
            this.registerComponentCallbacks(mBackgroundForegroundHandler);
        }
    }

    public void unregisterComponentCallbacks() {
        synchronized (mBackgroundForegroundHandler) {
            this.unregisterComponentCallbacks(mBackgroundForegroundHandler);
        }
    }

    @Override
    public void onBackground() {
        if (mIsBackground) {
            return;
        }

        mIsBackground = true;
        // do something
    }

    @Override
    public void onForeground() {
        if (mIsBackground) {
            mIsBackground = false;

            // do something
        }
    }
}

```

BackgroundForegroundHandler.class 用于捕获 Foreground 和 Background 的监听，这个类在 Applicatin 中初始化。

```java
public class BackgroundForegroundHandler implements Application.ActivityLifecycleCallbacks, ComponentCallbacks2 {

    private static final String TAG = BackgroundForegroundHandler.class.getSimpleName();

    private BackgroundForegroundDelegate mDelegate;

    public BackgroundForegroundHandler(BackgroundForegroundDelegate delegate) {
        mDelegate = delegate;
    }

    @Override
    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
    }

    @Override
    public void onActivityStarted(Activity activity) {

    }

    @Override
    public void onActivityResumed(Activity activity) {
        Log.d(TAG, "onActivityResumed: ");
        mDelegate.onForeground();
    }

    @Override
    public void onActivityPaused(Activity activity) {

    }

    @Override
    public void onActivityStopped(Activity activity) {

    }

    @Override
    public void onActivitySaveInstanceState(Activity activity, Bundle outState) {

    }

    @Override
    public void onActivityDestroyed(Activity activity) {

    }

    @Override
    public void onTrimMemory(int level) {
        if (level == ComponentCallbacks2.TRIM_MEMORY_UI_HIDDEN) {
            mDelegate.onBackground();
        }
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {

    }

    @Override
    public void onLowMemory() {

    }
}
```


# 使用 onTrimMemory 实现 Background 监听

使用方法参考：[https://www.jianshu.com/p/9e19f48c90ee](https://www.jianshu.com/p/9e19f48c90ee)

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

    public ComponentCallbacks2 mTrimMemoryCallback = new ComponentCallbacks2() {

        @Override
        public void onConfigurationChanged(Configuration newConfig) {

        }

        @Override
        public void onLowMemory() {

        }

        @Override
        public void onTrimMemory(int level) {
            if (level == ComponentCallbacks2.TRIM_MEMORY_UI_HIDDEN) {
                App.getInstance().onBackground();
            }
        }
    };
```


# 利用 Application.ActivityLifecycleCallbacks 实现 Foreground 监听 （推荐）

一个类实现 `Application.ActivityLifecycleCallbacks` 接口，在 Application 中注册。

就能获取 Activiyt onResume 的监听。


# 利用 BaseActivity 实现 Foreground 监听（不推荐）

BaseActivity.class

```java
    @Override
    protected void onResume() {
        super.onResume();
        App.getInstance().onForeground();
    }
```

# 使用 Android Architecture Components 实现

```java
class ArchLifecycleApp : Application(), LifecycleObserver {

    override fun onCreate() {
        super.onCreate()
        ProcessLifecycleOwner.get().lifecycle.addObserver(this)
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    fun onAppBackgrounded() {
        Log.d("Awww", "App in background")
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    fun onAppForegrounded() {
        Log.d("Yeeey", "App in foreground")
    }

}
```


。。。

参考：[https://android.jlelse.eu/how-to-detect-android-application-open-and-close-background-and-foreground-events-1b4713784b57](https://android.jlelse.eu/how-to-detect-android-application-open-and-close-background-and-foreground-events-1b4713784b57)
