---
title: EventBus肢解-事件队列
date: 2017-09-24 09:38:14
tags: [Source Code]
categories: android

---



```java
public class MainActivity extends AppCompatActivity {

    private static final String TAG = MainActivity.class.getSimpleName();

    private boolean mToggle = true;
    private DealEventThread mDealEventThread;
    private volatile int cnt = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mMyPostQueue = new MyPostQueue();
        executorService = Executors.newCachedThreadPool();
    }

    public void startMyLogger(View view) {

        mToggle = true;
        new Thread(new AddEventThread()).start();

    }

    public void stopMyLooper(View view) {
        mToggle = false;
    }


    // 事件队列
    private MyPostQueue mMyPostQueue;

    class AddEventThread implements Runnable {

        @Override
        public void run() {
            while (mToggle){
                MyPost myPost = MyPost.obtainMyPost(cnt++ + "");
                synchronized (MainActivity.this) {
                    mMyPostQueue.enqueue(myPost);
                    Log.d(TAG, "run: add event" + myPost.mMsg);
                    if (!executorRunning) {
                        executorRunning = true;
                        if (mDealEventThread==null) {
                            mDealEventThread = new DealEventThread();
                        }
                        executorService.execute(mDealEventThread);
                    }

                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

            }

        }
    }

    private static ExecutorService executorService;
    private volatile boolean executorRunning;

    class DealEventThread implements Runnable {

        @Override
        public void run() {
            try {
                while (true) {

                    MyPost pendingPost = mMyPostQueue.poll();
                    if (pendingPost == null) {
                        synchronized (this) {
                            // Check again, this time in synchronized
                            pendingPost = mMyPostQueue.poll();
                            if (pendingPost == null) {
                                executorRunning = false;
                                return;
                            }
                        }
                    }

                    Thread.sleep(1000);

                    // deal
                    Log.d(TAG, "run: deal event:" + pendingPost.mMsg);
                    //...
                }

            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                executorRunning = false;
            }
        }
    }

}
```