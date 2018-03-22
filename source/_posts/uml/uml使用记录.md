---
title: UML 使用记录
date: 2017-09-05 13:18:12
tags: [uml]
category: readnote

---


# 继承 泛化关系 generalization

实线-空心三角形


# 实现

接口实现

虚线-空心三角形


# 依赖 Dependency

这种关系具有偶然性和临时性

在一个类中使用到了另一个类

虚线-箭头

比如

- A 类中的一个方法参数中有 B 类

<!--more-->

# 关联 association

强依赖关系

实线-箭头


比如

- A 类中的成员变量是 B 类的对象

# 聚合 aggregation

是关联关系的一种特例，提现的是整体与部分，拥有的关系，即 has-a 的关系

空心矩形-实线-箭头

比如：

- 雁群-大雁


# 组合 composition

是关联关系的一种特例，提现 contains-a 的关系，这种关系比聚合更强，也称为 强聚合。

整体与部分是不可分的，整体的生命周期结束也就意味部分的生命周期结束。

实心矩形-实线-箭头

比如：

- 大雁-翅膀

关系所表现的强弱程度依次为：组合>聚合>关联>依赖



参考：

- [http://www.cnblogs.com/olvo/archive/2012/05/03/2481014.html](http://www.cnblogs.com/olvo/archive/2012/05/03/2481014.html)
- [https://www.jianshu.com/p/ee0c26bcbf3d](https://www.jianshu.com/p/ee0c26bcbf3d)

