---
title: Handler消息机制解析
date: 2016-03-23 21:38:14
tags: Handler
categories: android

---


# Handler消息机制解析

Android 提供用来更新UI的一套机制，也是一套消息处理机制，可以发送消息和处理消息。

Handler 提供了线程之间通信的方法。


## Handler
>Handler 对象来与 Looper 沟通，以便 push 新消息到 MessageQueue 里;或者接收 Looper 从 Message Queue 取出所送来的消息。

    final MessageQueue mQueue;
    final Looper mLooper;
    final Callback mCallback;


### 方法
1. 构造方法

```java
		public Handler(Callback callback, boolean async) {
        	if (FIND_POTENTIAL_LEAKS) {
            	final Class<? extends Handler> klass = getClass();
            	if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                    (klass.getModifiers() & Modifier.STATIC) == 0) {
                Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                    klass.getCanonicalName());
            	}
        	}

        	mLooper = Looper.myLooper();
        	if (mLooper == null) {
            	throw new RuntimeException(
                "Can't create handler inside thread that has not called Looper.prepare()");
        	}
        	mQueue = mLooper.mQueue;
        	mCallback = callback;
        	mAsynchronous = async;
    	}
```

几个构造方法最终都会调用这个构造方法，`myLooper()`。获取当前Looper对象，通过looper获取MessageQueue，完成了handler和looper的关联

2. 消息发送方法

```java
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
```

如果队列为`null`就抛异常，否则向队列中放入消息。

```java
	    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        	msg.target = this;
        	if (mAsynchronous) {
            	msg.setAsynchronous(true);
        	}
        	return queue.enqueueMessage(msg, uptimeMillis);
    	}
```
放入消息的时候要指定目标，默认是发送个自己。

消息放入队列后，Looper通过`looper()`处理消息队列

3. 分发消息方法

```java
	 	/**
     	* Handle system messages here.
     	*/
    	public void dispatchMessage(Message msg) {
        	if (msg.callback != null) {
            	handleCallback(msg);
        	} else {
            	if (mCallback != null) {
                	if (mCallback.handleMessage(msg)) {
                    	return;
                	}
            	}
            	handleMessage(msg);
        	}
    	}
```

在这个方法内调用`handleCallback`方法处理消息

### 内部接口`Callback`

```java
	public interface Callback {
        public boolean handleMessage(Message msg);
    }
```
	
## Looper

>一个线程只能产生一个Looper对象，由它来管理此线程里的MessageQueue(消息队列)。 

1. 内部包含了一个消息队列`MessageQueue`所有handler发送的消息都通过这个队列。
2. Looper.Looper方法是一个死循环，不断从`MessageQueue`中取Message，有就处理，没有就阻塞


### Looper类的方法
1. Looper.prepare()方法（调用prepare(true)）
		
```java
		private static void prepare(boolean quitAllowed) {
			if (sThreadLocal.get() != null) {
				throw new RuntimeException("Only one Looper may be created per thread");
			}
			sThreadLocal.set(new Looper(quitAllowed));
		}
```

这个方法创建新的`Looper`，如果一个线程中已经有一个Looper就会报错，如果没有就调用私有构造方法创建一个新的`Looper`

2. 私有的构造方法

```java	
		  private Looper(boolean quitAllowed) {
				mQueue = new MessageQueue(quitAllowed);
				mThread = Thread.currentThread();
			} 
```

创建了一个`MessageQueue`，并关联当前`Thread`

3. `myLopper()`

```java
		/**
     	* Return the Looper object associated with the current thread.  Returns
     	* null if the calling thread is not associated with a Looper.
     	*/
    	public static @Nullable Looper myLooper() {
        	return sThreadLocal.get();
    	}
```

获取当前Looper对象，

4. `myQueue()`

```java
    	/**
     	* Return the {@link MessageQueue} object associated with the current
     	* thread.  This must be called from a thread running a Looper, or a
     	* NullPointerException will be thrown.
     	*/
    	public static @NonNull MessageQueue myQueue() {
        	return myLooper().mQueue;
    	}
```

获取Looper中的MessageQueue

5. 死循环方法 loop()

```java
    	/**
    	 * Run the message queue in this thread. Be sure to call
		 * {@link #quit()} to end the loop.
		 */
		public static void loop() {
			final Looper me = myLooper();
			if (me == null) {
				throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
			}
			final MessageQueue queue = me.mQueue;

        	// Make sure the identity of this thread is that of the local process,
        	// and keep track of what that identity token actually is.
        	Binder.clearCallingIdentity();
        	final long ident = Binder.clearCallingIdentity();

        for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            msg.target.dispatchMessage(msg);

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

            // Make sure that during the course of dispatching the
            // identity of the thread was not corrupted.
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }

            msg.recycleUnchecked();
       	 }
    	}
```

这个方法用于处理队列中的消息：通过myLooper()方法获取单前的`Looper`，从而获取当前的消息队列，通过消息队列的`next()`方法获取消息，消息不为null的时候调用handler的`dispatchMessage(msg)`方法
	
## MessageQueue消息队列

>存储消息的容器


### 内部类 Values
 

# Handler 如何实现 postDelay 延时事件

1. 向 MessageQueue 中插入 message

插入 message, 的过程中是有排序的

2. `loop` 循环，通过调用 `queue.next()` 读取下个事件

读取下个事件的时候，发现是延时事件，则阻塞在 `queue.next()` 当前循环

记录延时时间，到时间后激活代码 `nativeWake(mPtr)（唤起线程）` ，返回 Message

3. 当有新的非延迟的 message 加入到 `MessageQueue` 的时候，会自动取消阻塞，自动获取最新的 message






