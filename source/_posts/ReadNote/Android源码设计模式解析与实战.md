---
title: Android源码设计模式解析与实战
date: 2016-10-16 13:18:12
tags: [设计模式, Android]
category: 读书笔记

---

原文： [https://github.com/simple-android-framework/android_design_patterns_analysis](https://github.com/simple-android-framework/android_design_patterns_analysis)

# 单例模式

确保一个类只有一个实例， 通常是为了是因为这个类需要消耗太多资源或者没有多个实例的需要。

最简单的设计模式

## 单例的实现

    public class CEO extends Person{

        private static final CEO mInstance = new CEO();

        private CEO(){
        }

        public static CEO getInstance(){
            return mInstance;
        }

    }


<!--more-->

方式二：保证唯一性，也延迟了单例的是实例化

    public class CEO extends Person{

        public static CEO getInstanceFromHolder(){
            return SingletonHolder.mOnlyInstance;
        }

        private static class SingletonHolder{
            private static final CEO  mOnlyInstance = new CEO();
        }
    }

方式三：枚举单例，线程安全

    enum SingletonEnum{
        INSTANCE;
    }

方式四：注册到容器，根据key获取对象 （Android LayoutInflater就是）

    private static Map<String, Singleton=""> objMap = new HashMap<String, Singleton="">();

    // 注册对象到Map中
    public static void registerService(String key, Singleton instance){
        if(!objMap.containsKey(key)){
            objMap.put(key, instance);
        }
    }

    // 根据key获取单例对象
    public static Singleton getService(String key){
        return objMap.get(key);
    }

...

核心思想：私有化构造方法，通过静态方法获取对象的实例。

## Android 源码中的模式实现

在Android系统中，通过 Context 获取的系统级别的服务就是通过上面的方法四实现的单例。比如 WindowsManagerService, ActivityManagerService, LayoutInflater。。。

在 Android 中 Context 的数量为：Activity 个数 + Service 个数 + 1

# Builder 模式

将一个复杂对象的构建与它的表示分离，使同样的构建过程创建不同的表示。

使用场景：

1. 相同的方法，不同的执行顺序，产生不同的实践结果
2. 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同
3. 产品类非常复杂，或者产品类中的调用顺序不同产生不同的效能

## UML 类图

1. Product 产品类 : 产品的抽象类
2. Builder : 抽象类， 规范产品的组建，一般是由子类实现具体的组件过程。
3. ConcreteBuilder : 具体的构建器.
4. Director : 统一组装过程(可省略)。

## 例子 DialogBuilder

Builder同时扮演了上文中提到的builder、ConcreteBuilder、Director的角色，简化了Builder模式的设计。


# Proxy 模式

## 介绍

代理模式是对象的结构模式。代理模式给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用。

### 模式的使用场景

就是一个人或者机构代表另一个人或者机构采取行动。在一些情况下，一个客户不想或者不能够直接引用一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用。

## 模式基本实现

下面通过抽象的方式实现代理模式

### 源码

抽象对象角色

    public abstract class AbstractObject {
        //操作
        public abstract void operation();
    }

目标对象角色

    public class RealObject extends AbstractObject {
        @Override
        public void operation() {
            //一些操作
            System.out.println("一些操作");
        }
    }

代理对象角色

    public class ProxyObject extends AbstractObject{
        RealObject realObject = new RealObject();
        @Override
        public void operation() {
            //调用目标对象之前可以做相关操作
            System.out.println("before");        
            realObject.operation();        
            //调用目标对象之后可以做相关操作
            System.out.println("after");
        }
    }

客户端

    public class Client {
        public static void main(String[] args) {
            AbstractObject obj = new ProxyObject();
            obj.operation();
        }
    }

## 代理模式在Binder中的使用

Binder是Android中的一个类，它继承了IBinder接口

从IPC角度来说，Binder是Android中的一种跨进程通信方式，Binder还可以理解为一种虚拟的物理设备，它的设备驱动是/dev/binder，该通信方式在linux中没有；

从Android Framework角度来说，Binder是ServiceManager连接各种Manager（ActivityManager、WindowManager，etc）和相应ManagerService的桥梁；

从Android应用层来说，Binder是客户端和服务端进行通信的媒介，当你bindService的时候，服务端会返回一个包含了服务端业务调用的Binder对象，通过这个Binder对象，客户端就可以获取服务端提供的服务或者数据，这里的服务包括普通服务和基于AIDL的服务。

Binder一个很重要的作用是：将客户端的请求参数通过Parcel包装后传到远程服务端，远程服务端解析数据并执行对应的操作，同时客户端线程挂起，当服务端方法执行完毕后，再将返回结果写入到另外一个Parcel中并将其通过Binder传回到客户端，客户端接收到返回数据的Parcel后，Binder会解析数据包中的内容并将原始结果返回给客户端，至此，整个Binder的工作过程就完成了。由此可见，Binder更像一个数据通道，Parcel对象就在这个通道中跨进程传输，至于双方如何通信，这并不负责，只需要双方按照约定好的规范去打包和解包数据即可。

# Adapter 适配器模式

## 模式的定义
适配器模式把一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。

## 使用场景
用电源接口做例子，笔记本电脑的电源一般都是接受5V的电压，但是我们生活中的电线电压一般都是220V的输出。这个时候就出现了不匹配的状况，在软件开发中我们称之为接口不兼容，此时就需要适配器来进行一个接口转换。在软件开发中有一句话正好体现了这点：任何问题都可以加一个中间层来解决。这个层我们可以理解为这里的Adapter层，通过这层来进行一个接口转换就达到了兼容的目的。     

## 分类

1. 类适配器模式
2. 对象适配器模式

### 类适配器和对象适配器的权衡

　　*　　类适配器使用对象继承的方式，是静态的定义方式；而对象适配器使用对象组合的方式，是动态组合的方式。

　　*　　对于类适配器，由于适配器直接继承了Adaptee，使得适配器不能和Adaptee的子类一起工作，因为继承是静态的关系，当适配器继承了Adaptee后，就不可能再去处理Adaptee的子类了。对于对象适配器，一个适配器可以把多种不同的源适配到同一个目标。换言之，同一个适配器可以把源类和它的子类都适配到目标接口。因为对象适配器采用的是对象组合的关系，只要对象类型正确，是不是子类都无所谓。

　　*　  对于类适配器，适配器可以重定义Adaptee的部分行为，相当于子类覆盖父类的部分实现方法。对于对象适配器，要重定义Adaptee的行为比较困难，这种情况下，需要定义Adaptee的子类来实现重定义，然后让适配器组合子类。虽然重定义Adaptee的行为比较困难，但是想要增加一些新的行为则方便的很，而且新增加的行为可同时适用于所有的源。

　　*　　对于类适配器，仅仅引入了一个对象，并不需要额外的引用来间接得到Adaptee。对于对象适配器，需要额外的引用来间接得到Adaptee。

　　建议尽量使用对象适配器的实现方式，多用合成/聚合、少用继承。当然，具体问题具体分析，根据需要来选用实现方式，最适合的才是最好的。

## Android ListView中的Adapter模式

## 杂谈
### 优点
* 更好的复用性     
　　系统需要使用现有的类，而此类的接口不符合系统的需要。那么通过适配器模式就可以让这些功能得到更好的复用。

* 更好的扩展性     
　　在实现适配器功能的时候，可以调用自己开发的功能，从而自然地扩展系统的功能。

### 缺点
* 过多的使用适配器，会让系统非常零乱，不易整体进行把握。比如，明明看到调用的是A接口，其实内部被适配成了B接口的实现，一个系统如果太多出现这种情况，无异于一场灾难。因此如果不是很有必要，可以不使用适配器，而是直接对系统进行重构。

# Template

## 定义

定义一个操作中的算法的框架，而将一些步骤延迟到子类中。使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

## 模式的使用场景

1. 多个子类有公有的方法，并且逻辑基本相同时。
2. 重要、复杂的算法，可以把核心算法设计为模板方法，周边的相关细节功能则由各个子类实现。
3. 重构时，模板方法模式是一个经常使用的模式，把相同的代码抽取到父类中，然后通过钩子函数约束其行为。

## Android源码中的模式实现

AsyncTask

## 杂谈

优点

- 封装不变部分，扩展可变部分
- 提取公共部分代码，便于维护

缺点

- 模板方法会带来代码阅读的难度，会让心觉得难以理解。

# 外观模式(Facade)


## 模式介绍

### 模式的定义

外观模式(也成为门面模式)要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。它提供一个高层次的接口，使得子系统更易于使用。

### 模式的使用场景

- 在设计初期阶段，将不同的两个层分离；
- 在开发阶段，子系统往往因为不断的重构演化而变得越来越复杂，大多数的模式使用时也都会产生很多很小的类，这本是好事，但也给外部调用它们的用户程序带来了使用上的困难，增加外观Facade可以提供一个简单的接口，减少它们之间的依赖。
- 在维护一个遗留的大型系统时，可能这个系统已经非常难以维护和扩展了，但因为它包含非常重要的功能，新的需求开发必须依赖于它。


# 迭代器(Iterator)模式

## 模式介绍

### 模式的定义
迭代器（Iterator）模式，又叫做游标（Cursor）模式。GOF给出的定义为：提供一种方法访问一个容器（container）对象中各个元素，而又不需暴露该对象的内部细节。

### 模式的使用场景
Java JDK 1.2 版开始支持迭代器。每一个迭代器提供next()以及hasNext()方法，同时也支持remove()(1.8的时候remove已经成为default throw new UnsupportedOperationException("remove"))。对Android来说,集合Collection实现了Iterable接口,就是说,无论是List的一大家子还是Map的一大家子,我们都可以使用Iterator来遍历里面的元素,可以使用Iterator的集合

## 杂谈

### 优点

面向对象设计原则中的单一职责原则，对于不同的功能,我们要尽可能的把这个功能分解出单一的职责，不同的类去承担不同的职责。Iterator模式就是分离了集合对象的遍历行为，抽象出一个迭代器类来负责，这样不暴露集合的内部结构，又可让外部代码透明的访问集合内部的数据。

### 缺点

- 会产生多余的对象，消耗内存；
- 遍历过程是一个单向且不可逆的遍历
- 如果你在遍历的过程中,集合发生改变,变多变少,内容变化都是算,就会抛出来ConcurrentModificationException异常.

# 外观模式(Facade)

## 模式介绍

### 模式的定义

外观模式(也成为门面模式)要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。它提供一个高层次的接口，使得子系统更易于使用。

### 模式的使用场景

- 在设计初期阶段，将不同的两个层分离；
- 在开发阶段，子系统往往因为不断的重构演化而变得越来越复杂，大多数的模式使用时也都会产生很多很小的类，这本是好事，但也给外部调用它们的用户程序带来了使用上的困难，增加外观Facade可以提供一个简单的接口，减少它们之间的依赖。
- 在维护一个遗留的大型系统时，可能这个系统已经非常难以维护和扩展了，但因为它包含非常重要的功能，新的需求开发必须依赖于它。


# 策略模式


## 模式介绍

### 模式的定义

策略模式定义了一系列的算法，并将每一个算法封装起来，而且使它们还可以相互替换。策略模式让算法独立于使用它的客户而独立变化。

注：针对同一类型操作，将复杂多样的处理方式分别开来，有选择的实现各自特有的操作。

### 模式的使用场景

- 针对同一类型问题的多种处理方式，仅仅是具体行为有差别时。
- 需要安全的封装多种同一类型的操作时。
- 出现同一抽象多个子类，而又需要使用if-else 或者 switch-case来选择时。

## Android

属性动画中的 Interpolator


## 杂谈
策略模式主要用来分离算法，根据相同的行为抽象来做不同的具体策略实现。

通过以上也可以看出策略模式的优缺点：

优点：

* 结构清晰明了、使用简单直观。
* 耦合度相对而言较低，扩展方便。
* 操作封装也更为彻底，数据更为安全。

缺点：

* 随着策略的增加，子类也会变得繁多。
