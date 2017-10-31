---
title: Android内存优化 -- Handler
date: 2016-05-14 21:03:14
tags: [Android优化,Handler]
categories: android

---

# Android内存优化 -- Handler

## Handler 
在Activity中创建一个Handler的时候，IDE会出现个提示：

    This Handler class should be static or leaks might occur (com.example.androidtest.MainActivity.2)

原因：当Activity关闭时，Handler不一定处理完毕，但是Handler对Acitivty有引用关系，导致了GC无法回收Activity对象，造成内存泄露。
<!--more-->

### 为什么Handler对Activity会有引用
>看源码

- Handler是用来处理消息的，需要有对象进行消息的分发(Looper)
- 当进程启动的时候，ActivityThread会创建一个Looper对象
- Looper中有一个MessageQueue对象，保存着消息的队列
- 当Handler发送消息的时候，消息就被加入到MessageQueue中，Message的target对象和Handler进行了绑定，形成对Activity的引用，所以只有这个消息在，Activity就不会被回收。

    public boolean sendMessageAtTime(Message msg, long uptimeMillis) {  
        MessageQueue queue = mQueue;  
        if (queue == null) {  
            RuntimeException e = new RuntimeException(  
                    this + " sendMessageAtTime() called with no mQueue");  
            Log.w("Looper", e.getMessage(), e);  
            return false;  
        }  
        return enqueueMessage(queue, msg, uptimeMillis);  
    }  
      
    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {  
    //这里就是，消息持有了handler对象，handler持有Activity  
        msg.target = this;  
        if (mAsynchronous) {  
            msg.setAsynchronous(true);  
        }  
        return queue.enqueueMessage(msg, uptimeMillis);  
    }  

在Handler创建的时候，会关联上面创建的Looper对象，这样消息机制就运转起来了。

      public Handler(Callback callback, boolean async) {  
          if (FIND_POTENTIAL_LEAKS) {  
              final Class<? extends Handler> klass = getClass();  
        //看看这里，如果是匿名类或者成员类或者局部类，并且不是静态对象，系统就会提示警告，有内存泄露的危险  
              if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&  
                      (klass.getModifiers() & Modifier.STATIC) == 0) {  
                  Log.w(TAG, "The following Handler class should be static or leaks might occur: " +  
                      klass.getCanonicalName());  
              }  
          }  
    //取出Looper  
          mLooper = Looper.myLooper();  
          if (mLooper == null) {  
              throw new RuntimeException(  
                  "Can't create handler inside thread that has not called Looper.prepare()");  
          }  
          mQueue = mLooper.mQueue;  
          mCallback = callback;  
          mAsynchronous = async;  
      }  

## 避免Handler导致内存泄露该如何做

1. 用static声明handler，静态类不会引用外部类
2. 如果Handler中必须用到Activity，那就用WeakReference去引用
3. 在Activity结束或暂停的事件中，removeMessages或者removeCallbacksAndMessages将消息队列中的消息移除（避免满足上面两条后，当Activity关闭了，但是Handler还未处理到，造成内存泄露）


## 使用static Handler的例子


    public class SampleActivity extends Activity {
     
     /**
      - Instances of static inner classes do not hold an implicit
      - reference to their outer class.
      */
     private static class MyHandler extends Handler {
      private final WeakReference<SampleActivity> mActivity;
     
      public MyHandler(SampleActivity activity) {
       mActivity = new WeakReference<SampleActivity>(activity);
      }
     
      @Override
      public void handleMessage(Message msg) {
       SampleActivity activity = mActivity.get();
       if (activity != null) {
        // ...
       }
      }
     }
     
     private final MyHandler mHandler = new MyHandler(this);
     
     /**
      - Instances of anonymous classes do not hold an implicit
      - reference to their outer class when they are "static".
      */
     private static final Runnable sRunnable = new Runnable() {
       @Override
       public void run() { /* ... */ }
     };
     
     @Override
     protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
     
      // Post a message and delay its execution for 10 minutes.
      mHandler.postDelayed(sRunnable, 1000 * 60 * 10);
     
      // Go back to the previous Activity.
      finish();
     }
    }


在Activity onStop或者onDestroy的时候，取消掉该Handler对象的Message和Runnable。  通过查看Handler的API，它有几个方法：

    removeCallbacks(Runnable r)和removeMessages(int what)

或者

    //  If null, all callbacks and messages will be removed.
    mHandler.removeCallbacksAndMessages(null);

参考：[http://blog.csdn.net/bdmh/article/details/49251849](http://blog.csdn.net/bdmh/article/details/49251849)

