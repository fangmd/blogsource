---
title: MVP 学习
date: 2016-06-04 13:18:12
tags: [Android 架构]
category: android

---

# MVP 学习

## MVP 是什么
>一种设计模式

包含四个要素：

1. View：负责绘制UI元素，与用户进行交互(比如Activity)
2. View interface：需要View实现的接口，View通过View interface与Presenter进行交互，降低耦合，方便进行单元测试
3. Model：负责存储，检索，操作数据(有时也实现一个Model interface用来降低耦合);也有人理解为JavaBean；
4. Presenter：作为View与Model交互的中间纽带，处理与用户交互的负责逻辑

<!--more-->
## MVP优点
1. 逻辑代码转移：将原本在Activity中的复杂逻辑移到Presenter中，Activity只负责UI元素的初始化，建立UI元素与Presenter的关联（Listener之类），同时自己处理一点简单的逻辑。
2. 便于单元测试：在MVP模式中，处理复杂逻辑的Presenter是通过interface与View(Activity)进行交互的，说明我们可以通过自定义类实现这个interface来模拟Activity的行为对Presenter进行单元测试，省去了大量的部署及测试的时间。

## MVP与MVC比较

MVP：

- View不直接与Model交互，而是通过与Presenter交互来与Model间接交互
- Presenter与View的交互是通过接口来进行的，更有利于添加单元测试
- 通常View与Presenter是一对一的，但复杂的View可能绑定多个Presenter来处理逻辑    

MVC：

- View可以与Model直接交互
- Controller是基于行为的，并且可以被多个View共享
- 可以负责决定显示哪个View


## 使用

1. 给View设置一个接口，让Activity或者Fragment实现这个接口；
2. Presenter类，构造方法中传入，接口实现类(Activity/Fragment)；实现业务逻辑，实现业务逻辑的时候会调用View接口实现类中实现的方法；
3. 在Activity或者Fragment中创建Presenter类对象，调用Presenter类对象中的业务逻辑方法(Presenter类对象中的业务逻辑方法是由Activity/Fragment中实现的接口方法拼接出来的)
4. Model逻辑接口及其实现类


## 模仿Google demo 写的例子
原作者地址：[http://www.jianshu.com/p/14283d8d3a60](http://www.jianshu.com/p/14283d8d3a60)

### google demo todoapp 分析

1. 分包：按app功能点分包，每个包中通常有：`actviity`，`Contract`，`Fragment`，`Presenter`
2. activity作为连接`Fragment`,`Presenter`的中介，在`activity`中创建这两个对象，让`Fragment`实现`IView`接口，让`Presenter`实现`IPresenter`接口，在`Presenter`的构造方法中传入`Fragment`对象，关联view和model；在`Fragment`中通过`IView`的`setPresenter`方法获得当前的`Presenter`对象，做业务逻辑的实现；
