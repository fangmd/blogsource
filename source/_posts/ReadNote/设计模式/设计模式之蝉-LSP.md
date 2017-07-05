---
title: 设计模式之蝉 - LSP
date: 2016-03-29 21:38:14
tags: 设计模式
categories: 读书笔记

---

里氏替换原则：LSP （Liskov Substitution Principle）

定义: 有两种，第二种定义描述的更清晰

1. 如果每个类型为 S 的对象 o1，都有类型为 T 的对象 o2，使得 T 定义的所有程序 p 在所有的对象都换成 o2 时，程序 p 的行为没有发生变化，那么类型 s 是类型 T 的子类型。
2.  所有使用基类的地方必须能透明地使用其子类的对象

# 子类必须完全实现父类的方法

在做系统设计的时候，通常会定义一个接口或者抽象类，在编码的时候直接传入接口或者抽象类，这里就是用了里氏替换。

比如下面的代码结构：

![lps_0](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/lps_0.png)

加入一个玩具枪该如何设计：

![lps_1](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/lps_1.png)

>如果子类不能完整地实现父类的方法，或者父类的某些方法在子类中已经发生畸变，则建议断开父子继承关系，采用依赖，聚合，组合等关系代替继承。


<!--more-->

# 子类可以有自己的个性

![lps_2](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/lps_2.png)

狙击手使用的手枪必须是狙击枪，所以在狙击手没有传入接口，而是直接传入子类使用。

# 覆盖或实现父类的方法时输入参数可以被放大

# 覆写或实现父类的方法时输出结果可以被缩小













