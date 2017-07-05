---
title: UML 图
date: 2016-05-21 12:18:12
tags: UML
category: android

---

# UML

## 基本

### 类之间的关系

#### 泛化关系 generalization
类继承表现：泛化(generalize)和实现(realize)

##### 泛化 generalize
继承非抽象类

    直线+空心箭头

##### 实现关系 realize
继承抽象类

    虚线+空心箭头

#### 聚合关系 aggregation
实体对象之间的关系，表示整体由部分构成；与组合关系不同的是，整体和部分不是强依赖的，即使整体不存在了，部分仍然存在；

    直线+空心菱形

#### 组合关系 composition
但组合关系是一种强依赖的特殊聚合关系，如果整体不存在了，则部分也不存在了；

    直线+实心菱形

#### 关联关系(association)
描述不同类的对象之间的结构关系；它是一种静态关系， 通常与运行状态无关，一般由常识等因素决定的；它一般用来定义对象之间静态的、天然的结构； 所以，关联关系是一种“强关联”的关系；

    直线+[不闭合箭头]

成员变量

#### 依赖关系(dependency)
描述一个对象在运行期间会用到另一个对象的关系；

    虚线+不闭合箭头

注：在最终代码中，依赖关系体现为类构造方法及类方法的传入参数，箭头的指向为调用关系；依赖关系处理临时知道对方外，还是“使用”对方的方法和属性；

参考:[http://design-patterns.readthedocs.io/zh_CN/latest/read_uml.html](http://design-patterns.readthedocs.io/zh_CN/latest/read_uml.html)