---
title: Flutter 常用控件继承关系
date: 2018-09-14 13:18:12
tags: [Flutter]
category: Flutter

---

>目前为止常见的控件

```
- Widget
    - ProxyWidget
        - InheritedWidget
        - ParentDataWidget<Stack>
            - Positioned: 通常在 Stack 下使用
        - ParentDataWidget<Flex>
            - Flexible
            - Expanded: flex 控制比例
    - StatefulWidget
        - AnimatedSwitcher
        - BottomSheet
        - Navigator
        - TextField
        - Checkbox
        - Switch
        - Scaffold
        - AppBar
        - Scrollbar
        - WillPopScope
        - Hero
        - Image
        - WidgetsApp
        - TabBarView
        - PageView: SizedBox?
        - Localizations
        - TabBar TabBarView
        - SliverAppBar
    - StatelessWidget
        - Visibility
        - Text
        - Theme
        - Divider: 缺陷：只可以设置左边padding，可以参照源码自定义一个
        - Container
        - SafeArea
        - ScrollView
            - CustomScrollView
            - BoxScrollView
                - ListView
                - GridView
        - Dialog
        - Tab
        - Banner: 角落标签
            - CheckedModeBanner: 右上角 Debug 条纹
        - SliverSafeArea
        - TabPageSelector
        - SliverPersistentHeader
    - RenderObjectWidget: 可以创建 RenderObject，RenderObject 是具体绘制对象
        - LeafRenderObjectWidget
            - RichText
        - MultiChildRenderObjectWidget：多个子控件
            - Flex
            - Column
            - Row
            - Stack
            - Wrap
        - SingleChildRenderObjectWidget： 单一子控件
            - DecoratedBox
            - Padding
            - SizedBox
            - Opacity
            - Visibility
            - SliverPadding
            - Align
                - Center
            - FadeTransition
        - SliverMultiBoxAdaptorWidget
            - SliverList
            - SliverGrid
    


```