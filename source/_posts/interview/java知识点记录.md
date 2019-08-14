---
title: Java知识点记录
date: 2019-08-12 21:18:12
tags: [java]
category: java基础
---


# Object

## equals

1. 默认比较两个对象的地址值是否相同

## hashCode

返回一个 hash code 值。用于 hash tables.

性质：

1. 在一个Java应用的执行期间，如果一个对象提供给 equals 做比较的信息没有被修改的话，该对象多次调用hashCode() 方法，该方法必须始终如一返回同一个 integer。
2. 如果两个对象根据 equals(Object) 方法是相等的，那么调用二者各自的 hashCode() 方法必须产生同一个 integer 结果。
3. 并不要求根据 equals(Object) 方法不相等的两个对象，调用二者各自的 hashCode() 方法必须产生不同的 integer 结果。然而，程序员应该意识到对于不同的对象产生不同的 integer 结果，有可能会提高 hash table 的性能。


# String、StringBuffer、StringBuilder

- String是final类，不能被继承。对于已经存在的Stirng对象，修改它的值，就是重新创建一个对象
- StringBuffer是一个类似于String的字符串缓冲区，使用append()方法修改Stringbuffer的值，使用toString()方法转换为字符串，是线程安全的
- StringBuilder用来替代于StringBuffer，StringBuilder是非线程安全的，速度更快

# 内部类
- 内部类提供了更好的封装，可以把内部类隐藏在外部类之内，不允许同一个包中的其他类访问该类。
- 内部类的方法可以直接访问外部类的所有数据，包括私有的数据。

# 单例
## 饿汉式
```java
public class CustomManager {
    private Context mContext;
    private static final Object mLock = new Object();
    private static CustomManager mInstance;

    public static CustomManager getInstance(Context context) {
        synchronized (mLock) {
            if (mInstance == null) {
                mInstance = new CustomManager(context);
            }

            return mInstance;
        }
    }

    private CustomManager(Context context) {
        this.mContext = context.getApplicationContext();
    }
}
```
## 双重检查模式
```java
public class CustomManager {
    private Context mContext;
    private volatile static CustomManager mInstance;

    public static CustomManager getInstance(Context context) {
        // 避免非必要加锁
        if (mInstance == null) {
            synchronized (CustomManger.class) {
                if (mInstance == null) {
                    mInstacne = new CustomManager(context);
                }
            }
        }

        return mInstacne;
    }

    private CustomManager(Context context) {
        this.mContext = context.getApplicationContext();
    }
}
```

## 静态内部类模式
```java
public class CustomManager{
    private CustomManager(){
} 
    private static class CustomManagerHolder {
        private static final CustomManager INSTANCE = new CustomManager();
    }
 
    public static CustomManager getInstance() {
        return CustomManagerHolder.INSTANCE;
    } 
}
```
静态内部类的原理是：

当SingleTon第一次被加载时，并不需要去加载SingleTonHoler，只有当getInstance()方法第一次被调用时，才会去初始化INSTANCE，这种方法不仅能确保线程安全，也能保证单例的唯一性，同时也延迟了单例的实例化。getInstance()方法并没有多次去new对象，取的都是同一个INSTANCE对象。

虚拟机会保证一个类的``<clinit>()``方法在多线程环境中被正确地加锁、同步，如果多个线程同时去初始化一个类，那么只会有一个线程去执行这个类的``<clinit>()``方法，其他线程都需要阻塞等待，直到活动线程执行``<clinit>()``方法完毕

缺点在于无法传递参数，如Context等

# valatile

当把变量声明为volatile类型后，编译器与运行时都会注意到这个变量是共享的，因此不会将该变量上的操作与其他内存操作一起重排序。volatile变量不会被缓存在寄存器或者对其他处理器不可见的地方，JVM 保证了每次读变量都从内存中读，跳过 CPU cache 这一步，因此在读取volatile类型的变量时总会返回最新写入的值。

![](https://user-gold-cdn.xitu.io/2019/6/23/16b833f4a48b216e?w=550&h=429&f=png&s=21448)

当一个变量定义为 volatile 之后，将具备以下特性：
- 保证此变量对所有的线程的可见性，不能保证它具有原子性（可见性，是指线程之间的可见性，一个线程修改的状态对另一个线程是可见的）
- 禁止指令重排序优化
- volatile 的读性能消耗与普通变量几乎相同，但是写操作稍慢，因为它需要在本地代码中插入许多内存屏障指令来保证处理器不发生乱序执行

AtomicInteger 中主要实现了整型的原子操作，防止并发情况下出现异常结果，其内部主要依靠JDK 中的unsafe 类操作内存中的数据来实现的。volatile 修饰符保证了value在内存中其他线程可以看到其值得改变。CAS操作保证了AtomicInteger 可以安全的修改value 的值。

# 引用类型
强引用 > 软引用 > 弱引用 

| 引用类型 | 说明 |
|------|------|
| StrongReferenc（强引用）| 当一个对象具有强引用，那么垃圾回收器是绝对不会的回收和销毁它的，**非静态内部类会在其整个生命周期中持有对它外部类的强引用**|
| WeakReference （弱引用）| 在垃圾回收器运行的时候，如果对一个对象的所有引用都是弱引用的话，该对象会被回收 |
| SoftReference（软引用）| 如果一个对象只具有软引用，若内存空间足够，垃圾回收器就不会回收它；如果内存空间不足了，才会回收这些对象的内存|
| PhantomReference（虚引用） | 一个只被虚引用持有的对象可能会在任何时候被GC回收。虚引用对对象的生存周期完全没有影响，也无法通过虚引用来获取对象实例，仅仅能在对象被回收时，得到一个系统通知（只能通过是否被加入到ReferenceQueue来判断是否被GC，这也是唯一判断对象是否被GC的途径）。|











