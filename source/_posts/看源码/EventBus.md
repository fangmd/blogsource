---
title: EventBus 源码分析
date: 2017-09-15 09:38:14
tags: [Source Code]
categories: android

---


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

- Subscription: 封装了事件接收者（Object）和事件接收者内部事件接收方法（SubscriberMethod）
- EventBus 成员变量：`Map<Class<?>, CopyOnWriteArrayList<Subscription>> subscriptionsByEventType` 存储了 事件接收者 和 事件接收者相关的Subscription
- subscriptionsByEventType: 中会按照事件接收的优先级对 Subscription 进行排序
- eventType：【实际上就是 事件接收者.class】？？？？？？？？暂时不确定
- EventBus 成员变量：`Map<Object, List<Class<?>>> typesBySubscriber`: 保存 key-接收者对象， value：eventType（SubscriberMethod 的成员变量）
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



真正事件发送方法：

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

- `lookupAllEventTypes(Class<?> eventClass)` 方法通过 事件类 寻找 eventTypes【？（Class.getInterfaces）？？】
- EventBus 成员变量 `Map<Class<?>, List<Class<?>>> eventTypesCache` 实现 事件类-eventTypes 关联
- 


# EventBusBuilder.class

用于创建 EventBus 

里面保存的参数有：



# SubscriberMethod.class

对接收者接收方法的封装


# Subscription.class

封装了事件接收者（Object）和事件接收者内部事件接收方法（SubscriberMethod）


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


```java
    private final ThreadLocal<PostingThreadState> currentPostingThreadState = new ThreadLocal<PostingThreadState>() {
        @Override
        protected PostingThreadState initialValue() {
            return new PostingThreadState();
        }
    };
```

使用 ThreadLocal 实现线程内部的单例（保证一个线程中只有一个 PostingThreadState）





