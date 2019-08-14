---
title: ThreadLocal 源码
date: 2019-08-14 12:18:12
tags: 源码
category: android

---


# 作用

实现对象的线程单例。

例子：

```java
public final static ThreadLocal<AnimationHandler> sAnimatorHandler = new ThreadLocal<>();

public static AnimationHandler getInstance() {
  if (sAnimatorHandler.get() == null) {
    sAnimatorHandler.set(new AnimationHandler());
  }
  return sAnimatorHandler.get();
}
```

>在开发工程中通常很少用到，在第三方库中有使用到：Eventbus

# 源码

1. `Thread.class`

```java
/* ThreadLocal values pertaining to this thread. This map is maintained by the ThreadLocal class. */
ThreadLocal.ThreadLocalMap threadLocals = null;
```

`threadLocals`: 作为 Thread 成员变量, 存储对象。

2. `ThreadLocal.class`

```java
    /**
     * Get the map associated with a ThreadLocal. Overridden in
     * InheritableThreadLocal.
     *
     * @param  t the current thread
     * @return the map
     */
    ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
```

获取当前线程的 `threadLocals`: `ThreadLocalMap m = getMap(Thread.currentThread());`

3. `ThreadLocal.class`, 将对象存储到，`threadLocals` 中

```java
    /**
     * Sets the current thread's copy of this thread-local variable
     * to the specified value.  Most subclasses will have no need to
     * override this method, relying solely on the {@link #initialValue}
     * method to set the values of thread-locals.
     *
     * @param value the value to be stored in the current thread's copy of
     *        this thread-local.
     */
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
```

存储的时候，`ThreadLocal` 对象作为 key, 所以一个 `ThreadLocal` 只能存储一个对象。

4. `ThreadLocal.class` 获取存储的对象


```java
    /**
     * Returns the value in the current thread's copy of this
     * thread-local variable.  If the variable has no value for the
     * current thread, it is first initialized to the value returned
     * by an invocation of the {@link #initialValue} method.
     *
     * @return the current thread's value of this thread-local
     */
    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
```

# 总结

1. 对象最终存储在 Thread 对象成员变量 `ThreadLocal.ThreadLocalMap threadLocals` 中。
2. `ThreadLocal.ThreadLocalMap` 不是 Map ，但是和 Map 的用法和效果差不多。

