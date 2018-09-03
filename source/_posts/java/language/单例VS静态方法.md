---
title: 单例 VS 静态方法
date: 2018-01-22 21:18:12
tags: java
category: java基础
---

# 在 jvm 中存储区别

>Singleton objects are stored in Heap, but static objects are stored in stack.

单例：属于对象，存储在 堆 中

静态类，静态方法：存储在 栈 中

<!--more-->

# 设计层面考虑

单例：具有 面向对象 的特性，可以使用 继承 和 多态 实现更多的业务需求。

单例：可以实现 懒加载，在需要的时候在初始化

单例：在一些依赖注入框架中可以很好的管理单例对象


# 总结

对于一些重量级的对象通常使用 单例 来处理。

对于一些不涉及业务逻辑的方法使用 静态类 实现。


参考：

- [http://blog.csdn.net/johnny901114/article/details/11969015](http://blog.csdn.net/johnny901114/article/details/11969015)
- [https://stackoverflow.com/questions/519520/difference-between-static-class-and-singleton-pattern](https://stackoverflow.com/questions/519520/difference-between-static-class-and-singleton-pattern)