---
title: Flutter技术入门与实战
date: 2020-02-02 13:18:12
tags: [Flutter, 读书笔记]
category: 读书笔记
---

# 前言

跨平台方案:

1. Hybrid: 依赖于 WebView
2. React Native: 交接技术

利用 JavaScript Core 来做桥接，将 JavaScript 调用转化成 Native 调用。React Native 最终会生成对应的自定义原生控件。将框架本身和 App 开发者绑在系统的控件上，随着系统的升级和API的变化，开发者需要处理不同平台的差异。

3. Flutter 自己实现一套 UI 框架，直接在 GPU 上渲染 UI。

# 第 1 章 开启 Flutter 之旅

Flutter 包括一个现代的响应式框架，一个 2D 渲染引擎，现成的组件和开发工具。

## 一切皆为组件

1. 组件可以嵌套

有的组件负责定位，有的组件负责调整大小，有的组件负责渐变处理 ...

优点: 最大好处是解耦

缺点: 嵌套层次太多，复杂

2. 组件分为：有状态组件和无状态组件

无状态组件：属性不能改变

有状态组件：状态会在生命周期中发生改变

3. 通过 `@protected Widget build(BuildContext, context)` 函数构建组件

# 第 2 章 Flutter 基础知识

Flutter 程序入口: `main()` -> `runApp()`

## Flutter 主题

正常开发中很少用到。

1. 设置主题的方式: 给 MaterialApp 添加 theme 属性

```Dart
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
```

2. 可以创建局部主题
3. 主题使用方式(获取主题中的样式)

```
color: Theme.of(context).accentColor    // Color

style: Theme.of(context).textTheme.title // TextStyle
```

# 第 3 章 Dart 语言简述

Flutter 选择 Dart 的原因:

1. Dart 支持 AOT 编译模式。
2. Dart 支持 JIT 编译模式，在开发过程中实现 Hotload, 极大提升开发效率
3. Dart 可以轻松创建 62fps 运行的流程动画和专场。

Dart 可以在没有锁的情况下进行对象分配和垃圾回收。生产环境下执行机器码效率高。

4. Dart 支持声明式编程布局。
5. 易学，语法和现代语言基本类似。

## 泛型

作用：

1. 保证类型安全
2. 减少重复代码

## 元数据

>类似 Java 中的注解

1. `@deorecated`
2. `@override`
3. `@proxy`

# 第 4 章 常用组件

1. Container
2. Image
3. Text
4. Icon (不常用)
5. IconButton (不常用)
6. RaisedButton (Material Design Button 不常用)
7. ListView

支持水平和竖直

8. GridView
9. Form (不常用)

# 第 5 章 Material Design 风格组件

1. AppBar
2. AlertDialog
3. ButtonNavigationBar
4. Card
5. Drawer
6. FloatingActionButton
7. FlatButton (扁平按钮组件)
8. MateriaApp
9. PopupMenuButton
10. Scaffold: 脚手架组件，实现了基本的Material Design 布局
11. SnackBar
12. SimpleDialog
13. TabBar
14. TextField

## App 结构和导航组件

1. MaterialApp 应用组件

作用: 设置路由，设置应用名称(会显示在系统任务管理系中), 主题，首页，语言变化回调函数，导航观察器，是否显示 Debug 标签。

2. Scaffold

`resizeToAvoidBottomPadding`: 是否要重新布局来避免底部被覆盖.(虚拟键盘显示的时候)，默认为 true

# 第 6 章 Cupertino 风格组件

# 第 7 章 页面布局

1. Stack/Align
2. AspectRatio
2. Baseline
3. Center
4. Column
5. ConstainedBox: 限制 child 的宽高 最大值/最小值
6. Container
7. FittedBox: 缩放布局, 类似 ImageView 中的 ScaleType
8. FractionallySizedBox: 百分比布局
9. GridView
10. IndexedStack: 栈索引布局；基层自 Stack, 显示第 index 个 child, 其他 child 都不可见
11. LimitedBox: 限定宽高最大值
12. ListView
13. Offstate: 开关布局, 是否显示
14. OverflowBox: 溢出父容器显示，允许 child 超出父容器显示
15. Padding
16. Row
17. SizedBox: 给 child 设置制定宽高
18. Stack/Alignment
19. Stack/Positioned
20. Table
21. Transform
22. Wrap: 按宽高自动换行

# 第 8 章 手势

手势系统 2 层

1. 触摸原事件

PointerDownEvent, PointerMoveEvent, PointerUpEvent, PointerCancelEvent

2. 可以检测的手势 3 大类: 轻击，拖动，缩放


## GestureDetector 进行手势检测

## Dimissible 实现滑动删除

# 第 9 章 资源和图片

# 第 10 章 路由及导航

# 第 11 章 组件装饰和视觉效果

1. Opacity: 透明度
2. DecoratedBox

decoration 属性，BoxDecoration

2. RotatedBox: 旋转盒子，每次只能旋转90度
3. Clip 剪裁处理

ClipOval, ClipRRect, ClipPath

# 第 12 章

1. 透明度渐变效果: Animatedpacity
2. Hero 页面切换动画


# 第 15 章 测试与发布应用

