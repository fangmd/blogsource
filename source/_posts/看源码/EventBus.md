---
title: EventBus 源码分析
date: 2017-09-15 09:38:14
tags: [Source Code]
categories: android

---

可见的设计模式：

1. 单例模式
2. 构建者模式
3. 中介模式（EventBus 自己就是中介者）
4. 对象池模式 （pendingPostPool 延迟事件池）

数据结构知识：

1. 链表结构（PendingPostQueue 事件队列）


# EventBus.class

## 创建方法 是单例模式


```java
static volatile EventBus defaultInstance;

/** Convenience singleton for apps using a process-wide EventBus instance. */
public static EventBus getDefault() {
    if (defaultInstance == null) {
        synchronized (EventBus.class) {
            if (defaultInstance == null) {
                defaultInstance = new EventBus();
            }
        }
    }
    return defaultInstance;
}
```

创建方法使用了 构建者模式：

```java
// 自定义 构建者
public static EventBusBuilder builder() {
    return new EventBusBuilder();
}

// 默认的构造方法，里面使用了默认的构建者
private static final EventBusBuilder DEFAULT_BUILDER = new EventBusBuilder();
public EventBus() {
    this(DEFAULT_BUILDER);
}

EventBus(EventBusBuilder builder) {
    subscriptionsByEventType = new HashMap<>();
    typesBySubscriber = new HashMap<>();
    stickyEvents = new ConcurrentHashMap<>();
    mainThreadPoster = new HandlerPoster(this, Looper.getMainLooper(), 10);
    backgroundPoster = new BackgroundPoster(this);
    asyncPoster = new AsyncPoster(this);
    indexCount = builder.subscriberInfoIndexes != nulbuilder.subscriberInfoIndexes.size() : 0;
    subscriberMethodFinder = new SubscriberMethodFinder(builder.subscriberInfoIndexes,
            builder.strictMethodVerification, builder.ignoreGeneratedIndex);
    logSubscriberExceptions = builder.logSubscriberExceptions;
    logNoSubscriberMessages = builder.logNoSubscriberMessages;
    sendSubscriberExceptionEvent = builder.sendSubscriberExceptionEvent;
    sendNoSubscriberEvent = builder.sendNoSubscriberEvent;
    throwSubscriberException = builder.throwSubscriberException;
    eventInheritance = builder.eventInheritance;
    executorService = builder.executorService;
}
```


## 几个重要方法



### 注册接收者 register

```java
/**
 * Registers the given subscriber to receive events. Subscribers must call {@l#unregister(Object)} once they
 * are no longer interested in receiving events.
 * <p/>
 * Subscribers have event handling methods that must be annotated by {@link Subscribe}.
 * The {@link Subscribe} annotation also allows configuration like {@link
 * ThreadMode} and priority.
 */
public void register(Object subscriber) {
    Class<?> subscriberClass = subscriber.getClass();
    List<SubscriberMethod> subscriberMethodsubscriberMethodFinder.findSubscriberMethods(subscriberClass);
    synchronized (this) {
        for (SubscriberMethod subscriberMethod : subscriberMethods) {
            subscribe(subscriber, subscriberMethod);
        }
    }
}
```

注册一个事件的接收者。

- `List<SubscriberMethod> subscriberMethodsubscriberMethodFinder.findSubscriberMethods(subscriberClass);` 这个方法获取事件接收者类中定义的事件接收方法（使用 @Subscribe 注解的方法）
- SubscriberMethod：对接收者接收方法的封装

```java
    // Must be called in synchronized block
    private void subscribe(Object subscriber, SubscriberMethod subscriberMethod) {
        Class<?> eventType = subscriberMethod.eventType;
        Subscription newSubscription = new Subscription(subscriber, subscriberMethod);
        CopyOnWriteArrayList<Subscription> subscriptions = subscriptionsByEventType.get(eventType);
        if (subscriptions == null) {
            subscriptions = new CopyOnWriteArrayList<>();
            subscriptionsByEventType.put(eventType, subscriptions);
        } else {
            if (subscriptions.contains(newSubscription)) {
                throw new EventBusException("Subscriber " + subscriber.getClass() + " already registered to event "
                        + eventType);
            }
        }

        int size = subscriptions.size();
        for (int i = 0; i <= size; i++) {
            if (i == size || subscriberMethod.priority > subscriptions.get(i).subscriberMethod.priority) {
                subscriptions.add(i, newSubscription);
                break;
            }
        }

        List<Class<?>> subscribedEvents = typesBySubscriber.get(subscriber);
        if (subscribedEvents == null) {
            subscribedEvents = new ArrayList<>();
            typesBySubscriber.put(subscriber, subscribedEvents);
        }
        subscribedEvents.add(eventType);

        if (subscriberMethod.sticky) {
            if (eventInheritance) {
                // Existing sticky events of all subclasses of eventType have to be considered.
                // Note: Iterating over all events may be inefficient with lots of sticky events,
                // thus data structure should be changed to allow a more efficient lookup
                // (e.g. an additional map storing sub classes of super classes: Class -> List<Class>).
                Set<Map.Entry<Class<?>, Object>> entries = stickyEvents.entrySet();
                for (Map.Entry<Class<?>, Object> entry : entries) {
                    Class<?> candidateEventType = entry.getKey();
                    if (eventType.isAssignableFrom(candidateEventType)) {
                        Object stickyEvent = entry.getValue();
                        checkPostStickyEventToSubscription(newSubscription, stickyEvent);
                    }
                }
            } else {
                Object stickyEvent = stickyEvents.get(eventType);
                checkPostStickyEventToSubscription(newSubscription, stickyEvent);
            }
        }
    }
```

- Subscription: 封装了事件接收者（Object）和 事件接收者内部事件接收方法（SubscriberMethod）
- EventBus 成员变量：`Map<Class<?>, CopyOnWriteArrayList<Subscription>> subscriptionsByEventType` 存储了 事件 和 事件接收者相关的Subscription
- subscriptionsByEventType: 中会按照事件接收的优先级对 Subscription 进行排序
- eventType：实际上就是 事件.class
- EventBus 成员变量：`Map<Object, List<Class<?>>> typesBySubscriber`: 保存 key-接收者对象， value：eventType（事件.class）
- sticky 事件的实现 ？？？？




### unregister 取消注册

```java
    /** Unregisters the given subscriber from all event classes. */
    public synchronized void unregister(Object subscriber) {
        List<Class<?>> subscribedTypes = typesBySubscriber.get(subscriber);
        if (subscribedTypes != null) {
            for (Class<?> eventType : subscribedTypes) {
                unsubscribeByEventType(subscriber, eventType);
            }
            typesBySubscriber.remove(subscriber);
        } else {
            Log.w(TAG, "Subscriber to unregister was not registered before: " + subscriber.getClass());
        }
    }

    /** Only updates subscriptionsByEventType, not typesBySubscriber! Caller must update typesBySubscriber. */
    private void unsubscribeByEventType(Object subscriber, Class<?> eventType) {
        List<Subscription> subscriptions = subscriptionsByEventType.get(eventType);
        if (subscriptions != null) {
            int size = subscriptions.size();
            for (int i = 0; i < size; i++) {
                Subscription subscription = subscriptions.get(i);
                if (subscription.subscriber == subscriber) {
                    subscription.active = false;
                    subscriptions.remove(i);
                    i--;
                    size--;
                }
            }
        }
    }
```

- 从 `typesBySubscriber` 获取 接收者对应的 【eventType s】
- 通过 【eventType s】 从 `subscriptionsByEventType` 删除 eventType-List<Subscription>
- 从 `typesBySubscriber` 删除 接收者-【eventType s】


### 发送事件

```java
    /** Posts the given event to the event bus. */
    public void post(Object event) {
        PostingThreadState postingState = currentPostingThreadState.get();
        List<Object> eventQueue = postingState.eventQueue;
        eventQueue.add(event);

        if (!postingState.isPosting) {
            postingState.isMainThread = Looper.getMainLooper() == Looper.myLooper();
            postingState.isPosting = true;
            if (postingState.canceled) {
                throw new EventBusException("Internal error. Abort state was not reset");
            }
            try {
                while (!eventQueue.isEmpty()) {
                    postSingleEvent(eventQueue.remove(0), postingState);
                }
            } finally {
                postingState.isPosting = false;
                postingState.isMainThread = false;
            }
        }
    }
```

- EventBus 成员变量：`ThreadLocal<PostingThreadState> currentPostingThreadState`
- 这个方法中初始化了用于记录发送事件的线程的线程状态类：PostingThreadState



事件发送方法：

```
    private void postSingleEvent(Object event, PostingThreadState postingState) throws Error {
        Class<?> eventClass = event.getClass();
        boolean subscriptionFound = false;
        if (eventInheritance) {
            List<Class<?>> eventTypes = lookupAllEventTypes(eventClass);
            int countTypes = eventTypes.size();
            for (int h = 0; h < countTypes; h++) {
                Class<?> clazz = eventTypes.get(h);
                subscriptionFound |= postSingleEventForEventType(event, postingState, clazz);
            }
        } else {
            subscriptionFound = postSingleEventForEventType(event, postingState, eventClass);
        }
        if (!subscriptionFound) {
            if (logNoSubscriberMessages) {
                Log.d(TAG, "No subscribers registered for event " + eventClass);
            }
            if (sendNoSubscriberEvent && eventClass != NoSubscriberEvent.class &&
                    eventClass != SubscriberExceptionEvent.class) {
                post(new NoSubscriberEvent(this, event));
            }
        }
    }
```

- `lookupAllEventTypes(Class<?> eventClass)` 方法通过 事件类 寻找 eventTypes(事件类集合)
- EventBus 成员变量 `Map<Class<?>, List<Class<?>>> eventTypesCache` 实现 事件类-eventTypes 关联


事件不同线程之前切换代码：

```java
    private void postToSubscription(Subscription subscription, Object event, boolean isMainThread) {
        switch (subscription.subscriberMethod.threadMode) {
            case POSTING:
                invokeSubscriber(subscription, event);
                break;
            case MAIN:
                if (isMainThread) {
                    invokeSubscriber(subscription, event);
                } else {
                    mainThreadPoster.enqueue(subscription, event);
                }
                break;
            case BACKGROUND:
                if (isMainThread) {
                    backgroundPoster.enqueue(subscription, event);
                } else {
                    invokeSubscriber(subscription, event);
                }
                break;
            case ASYNC:
                asyncPoster.enqueue(subscription, event);
                break;
            default:
                throw new IllegalStateException("Unknown thread mode: " + subscription.subscriberMethod.threadMode);
        }
    }
```

### 事件队列

EventBus 成员变量：

```java
private final HandlerPoster mainThreadPoster;
private final BackgroundPoster backgroundPoster;
private final AsyncPoster asyncPoster;
```

- mainThreadPoster：存放所有需要在主线程中执行的事件。
- backgroundPoster：存放所有需要在子线程中执行的事件。（使用 ExecutorService 获取一个子线程让后执行，BackgroundPoster 实现了 Runnable 接口）
- asyncPoster：存放所有需要在子线程中执行的事件（使用 ExecutorService 获取一个子线程让后执行，AsyncPoster 实现了 Runnable 接口）


#### BackgroundPoster vs AsyncPoster

区别

- 从事件队列中取事件的时候：BackgroundPoster 采用延迟取事件（1000），AsyncPoster 马上取事件


#### HandlerPoster.class

##### 构造方法

是 Handler 的子类，在 EventBus 中创建，创建时使用 mainLopper 创建：

```java
mainThreadPoster = new HandlerPoster(this, Looper.getMainLooper(), 10);

//...
HandlerPoster(EventBus eventBus, Looper looper, int maxMillisInsideHandleMessage)

```

##### 成员变量

```java
    private final PendingPostQueue queue;
    private final int maxMillisInsideHandleMessage;
    private final EventBus eventBus;
    private boolean handlerActive;
```

- queue: 需要在主线程执行的事件队列
- eventBus：EventBus 对象
- handlerActive：记录是否有事件正在执行
- maxMillisInsideHandleMessage：超时记录

##### 向队列中添加一个事件

```java
    void enqueue(Subscription subscription, Object event) {
        PendingPost pendingPost = PendingPost.obtainPendingPost(subscription, event);
        synchronized (this) {
            queue.enqueue(pendingPost);
            if (!handlerActive) {
                handlerActive = true;
                if (!sendMessage(obtainMessage())) {
                    throw new EventBusException("Could not send handler message");
                }
            }
        }
    }
```

- 通过 Subscription（事件接收者Object 和 事件接收者内部事件处理方法SubscriberMethod 的封装） 创建一个 PendingPost （延迟事件）
- handlerActive：如果为 true 表示事件队列的处理机制已经启动（这个机制会在 事件队列queue 中的所有事件被处理完成后停止），如果为 false 表示当前事件处理机制处于停止状态。
- `queue.enqueue(pendingPost);`: 向队列中加入一个 延迟事件
- `sendMessage(obtainMessage())`: 启动事件处理机制


##### handleMessage

```java
    @Override
    public void handleMessage(Message msg) {
        boolean rescheduled = false;
        try {
            long started = SystemClock.uptimeMillis();
            while (true) {
                PendingPost pendingPost = queue.poll();
                if (pendingPost == null) {
                    synchronized (this) {
                        // Check again, this time in synchronized
                        pendingPost = queue.poll();
                        if (pendingPost == null) {
                            handlerActive = false;
                            return;
                        }
                    }
                }
                eventBus.invokeSubscriber(pendingPost);
                long timeInMethod = SystemClock.uptimeMillis() - started;
                if (timeInMethod >= maxMillisInsideHandleMessage) {
                    if (!sendMessage(obtainMessage())) {
                        throw new EventBusException("Could not send handler message");
                    }
                    rescheduled = true;
                    return;
                }
            }
        } finally {
            handlerActive = rescheduled;
        }
    }
```

- maxMillisInsideHandleMessage：内部定义为 10
- rescheduled：表示本次循环处理了一个事件


#### BackgroundPoster.class

实线了 Runnable 接口

##### 成员变量

```java
    private final PendingPostQueue queue;
    private final EventBus eventBus;

    private volatile boolean executorRunning;
```

- queue: 事件队列
- EventBus
- executorRunning： 事件处理机制（一个死循环）是否正在执行


##### 添加事件

```java
    public void enqueue(Subscription subscription, Object event) {
        PendingPost pendingPost = PendingPost.obtainPendingPost(subscription, event);
        synchronized (this) {
            queue.enqueue(pendingPost);
            if (!executorRunning) {
                executorRunning = true;
                eventBus.getExecutorService().execute(this);
            }
        }
    }
```

同样的需要将 Subscription + event = PendingPost （将事件相关信息进行封装，让后放入事件队列中处理）

添加事件后启动事件处理机制

##### 事件处理机制（死循环）

```java
    @Override
    public void run() {
        try {
            try {
                while (true) {
                    PendingPost pendingPost = queue.poll(1000);
                    if (pendingPost == null) {
                        synchronized (this) {
                            // Check again, this time in synchronized
                            pendingPost = queue.poll();
                            if (pendingPost == null) {
                                executorRunning = false;
                                return;
                            }
                        }
                    }
                    eventBus.invokeSubscriber(pendingPost);
                }
            } catch (InterruptedException e) {
                Log.w("Event", Thread.currentThread().getName() + " was interruppted", e);
            }
        } finally {
            executorRunning = false;
        }
    }
```


- `PendingPost pendingPost = queue.poll(1000);`: 延迟 1s 从队列中取事件


#### AsyncPoster.class

```java
class AsyncPoster implements Runnable {

    private final PendingPostQueue queue;
    private final EventBus eventBus;

    AsyncPoster(EventBus eventBus) {
        this.eventBus = eventBus;
        queue = new PendingPostQueue();
    }

    public void enqueue(Subscription subscription, Object event) {
        PendingPost pendingPost = PendingPost.obtainPendingPost(subscription, event);
        queue.enqueue(pendingPost);
        eventBus.getExecutorService().execute(this);
    }

    @Override
    public void run() {
        PendingPost pendingPost = queue.poll();
        if(pendingPost == null) {
            throw new IllegalStateException("No pending post available");
        }
        eventBus.invokeSubscriber(pendingPost);
    }

}
```

和 BackgroundPoster 类似




### 接收者处理事件的方法

```java
    void invokeSubscriber(Subscription subscription, Object event) {
        try {
            subscription.subscriberMethod.method.invoke(subscription.subscriber, event);
        } catch (InvocationTargetException e) {
            handleSubscriberException(subscription, event, e.getCause());
        } catch (IllegalAccessException e) {
            throw new IllegalStateException("Unexpected exception", e);
        }
    }
```

- `method.invoke` 调用接收者的事件接收方法（类似反射）


延迟事件处理：

```java
    void invokeSubscriber(PendingPost pendingPost) {
        Object event = pendingPost.event;
        Subscription subscription = pendingPost.subscription;
        PendingPost.releasePendingPost(pendingPost);
        if (subscription.active) {
            invokeSubscriber(subscription, event);
        }
    }
```

## 重要的成员变量

```java
    private static final EventBusBuilder DEFAULT_BUILDER = new EventBusBuilder();
    private static final Map<Class<?>, List<Class<?>>> eventTypesCache = new HashMap<>();

    private final Map<Class<?>, CopyOnWriteArrayList<Subscription>> subscriptionsByEventType;
    private final Map<Object, List<Class<?>>> typesBySubscriber;
    private final Map<Class<?>, Object> stickyEvents;

    private final ThreadLocal<PostingThreadState> currentPostingThreadState = new ThreadLocal<PostingThreadState>() {
        @Override
        protected PostingThreadState initialValue() {
            return new PostingThreadState();
        }
    };

    private final HandlerPoster mainThreadPoster;
    private final BackgroundPoster backgroundPoster;
    private final AsyncPoster asyncPoster;
    private final SubscriberMethodFinder subscriberMethodFinder;
    private final ExecutorService executorService;
```

- eventType: 实际上是 被发送的事件类 (CustomEvent.class)
- DEFAULT_BUILDER: 默认的构建者对象
- eventTypesCache：缓存 EventType(事件类.class) 和 EventType子类，EventType相关接口类
- subscriptionsByEventType：记录 EventType(事件类) 和 事件接收者 之间的联系
- typesBySubscriber： 记录 事件接收对象（Object）和 EventType（事件类.class）
- stickyEvents： 记录粘性事件 
- currentPostingThreadState：记录线程状态
- mainThreadPoster：主线程事件队列管理类（保存事件队列，有从事件队列中取事件给 EventBus 处理的功能）
- backgroundPoster：Background 事件类型的事件队列管理类
- AsyncPoster：Async 事件类型的事件队列管理类
- subscriberMethodFinder： 从 事件接收者 中解析 事件接收方法（加了 @Subscribe 注解的方法），获取 SubscriberMethod
- executorService: 相当于线程池的作用，执行所有在子线程中执行的事件


# EventBusBuilder.class

用于创建 EventBus 

里面保存的参数有：



# SubscriberMethod.class

对接收者接收方法的封装

成员熟悉：

```java
    final Method method;
    final ThreadMode threadMode;
    final Class<?> eventType;
    final int priority;
    final boolean sticky;
    /** Used for efficient comparison */
    String methodString;
```

- method: 接收者中的事件接收方法，在执行事件处理的时候调用
- threadMode：记录接受者事件接收方法在哪个线程中执行
- priority：优先级
- sticky：是否是粘性事件接收方法
- methodString：用来对比两个接收方法是否是同一个


# Subscription.class

封装了事件接收者（Object）和 事件接收者内部事件接收方法（SubscriberMethod）

成员变量

```java
    final Object subscriber;
    final SubscriberMethod subscriberMethod;
```

- subscriber: 事件接收者
- subscriberMethod：事件接收者中的事件接收方法



# PostingThreadState.class

作用：

1. 里面有事件队列：EventBus 发送的数据存储在队列中 `eventQueue`
2. 记录线程状态

```java
    /** For ThreadLocal, much faster to set (and get multiple values). */
    final static class PostingThreadState {
        final List<Object> eventQueue = new ArrayList<Object>();
        boolean isPosting;
        boolean isMainThread;
        Subscription subscription;
        Object event;
        boolean canceled;
    }
```

- isMainThread：记录当前线程是不是主线程（在 EventBus post 方法中初始化）
- isPosting： 是否正在发送事件
- eventQueue：记录当前线程所有需要被处理的 事件
- subscription: 记录当前正在被处理的事件，的事件接收者
- event：记录当前正在被处理的 事件对象
- canceled：当前的处理是否被取消


```java
    private final ThreadLocal<PostingThreadState> currentPostingThreadState = new ThreadLocal<PostingThreadState>() {
        @Override
        protected PostingThreadState initialValue() {
            return new PostingThreadState();
        }
    };
```

使用 ThreadLocal 实现线程内部的单例（保证一个线程中只有一个 PostingThreadState）


# ExecutorService.class

表述了异步执行的机制，并且可以让任务在后台执行。 类似一个线程池。


# PendingPost.class

一个延迟事件，不被马上处理的事件都会被封装成 PendingPost 对象，让后被放置到指定的 事件队列 中等待处理。

Subscription -》PendingPost

```java
    static PendingPost obtainPendingPost(Subscription subscription, Object event) {
        synchronized (pendingPostPool) {
            int size = pendingPostPool.size();
            if (size > 0) {
                PendingPost pendingPost = pendingPostPool.remove(size - 1);
                pendingPost.event = event;
                pendingPost.subscription = subscription;
                pendingPost.next = null;
                return pendingPost;
            }
        }
        return new PendingPost(event, subscription);
    }
```

延迟事件被处理的时候需要先从 PendingPost 中获取 Subscription：

```java
Subscription subscription = pendingPost.subscription;
```

然后释放延迟事件：

```java
    static void releasePendingPost(PendingPost pendingPost) {
        pendingPost.event = null;
        pendingPost.subscription = null;
        pendingPost.next = null;
        synchronized (pendingPostPool) {
            // Don't let the pool grow indefinitely
            if (pendingPostPool.size() < 10000) {
                pendingPostPool.add(pendingPost);
            }
        }
    }
```

## 构造方法

```java
    private PendingPost(Object event, Subscription subscription) {
        this.event = event;
        this.subscription = subscription;
    }
```

是私有的，只能通过静态方法创建 PendingPost

## 成员变量

```java
private final static List<PendingPost> pendingPostPool = new ArrayList<PendingPost>();

    Object event;
    Subscription subscription;
    PendingPost next;
```

- pendingPostPool: 延迟事件池，较少不断创建对象对性能的消耗。
- event：是用户发送的  事件对象


# PendingPostQueue.class 事件队列的实现


```java
final class PendingPostQueue {
    private PendingPost head;
    private PendingPost tail;

    synchronized void enqueue(PendingPost pendingPost) {
        if (pendingPost == null) {
            throw new NullPointerException("null cannot be enqueued");
        }
        if (tail != null) {
            tail.next = pendingPost;
            tail = pendingPost;
        } else if (head == null) {
            head = tail = pendingPost;
        } else {
            throw new IllegalStateException("Head present, but no tail");
        }
        notifyAll();
    }

    synchronized PendingPost poll() {
        PendingPost pendingPost = head;
        if (head != null) {
            head = head.next;
            if (head == null) {
                tail = null;
            }
        }
        return pendingPost;
    }

    synchronized PendingPost poll(int maxMillisToWait) throws InterruptedException {
        if (head == null) {
            wait(maxMillisToWait);
        }
        return poll();
    }

}
```

单链结构


- 当队列中只有一个事件的时候，head，tail 指向同一个事件对象 `head = tail = pendingPost;`，它们的 next 都指向 null
- 当队列中事件 = 2 的时候，head.next 指向第二个事件，tail.next 指向 自己
- 当队列中事件 >2 的时候，最后一个事件（tail.next 指向 自己）, 中间的事件(mid.next 指向下一个事件)

# SubscriberMethodFinder

作用：从事件接收者中获取所有的事件接收方法 得到 `List<SubscriberMethod>`

通过反射的方法获取 【事件接收类】 中所有的方法，然后遍历所有的方法，获取方法的注解，通过注解判断是否是 【事件处理方法】。





