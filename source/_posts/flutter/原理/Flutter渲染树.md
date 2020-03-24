---
title: Flutter 渲染树
date: 2020-03-23 13:18:12
tags: [Flutter]
category: Flutter

---


# Widget

```Dart
@immutable
abstract class Widget extends DiagnosticableTree {
```

Widget 属于 `@immutable` 不可变的。属于轻量级对象，不断销毁创建不会影响性能。

作用：描述页面信息，并且生成 `Element` 对象, 不可变的 Widget 树生成可变的 Element 树。


## 类继承结构

类继承结构

```
- Widget
    - StatelessWidget
    - StatefulWidget
    - RenderObjectWidget
    - ProxyWidget
        - InheritedWidget
```

# Element

## 类继承结构

```
- Element
    - ComponentElement
        - StatefulElement
        - StatelessElement
        - ProxyElement
            - InheritedElement
    - RenderObjectElement
```

## Widget -> Element

Widget 转 Element 位置: `runApp()` -> `scheduleAttachRootWidget` -> `WidgetBinding.attachRootWidget(Widget)`:

```Dart
  void attachRootWidget(Widget rootWidget) {
    _renderViewElement = RenderObjectToWidgetAdapter<RenderBox>(
      container: renderView,
      debugShortDescription: '[root]',
      child: rootWidget,
    ).attachToRenderTree(buildOwner, renderViewElement);
  }
```

## 设置成需要更新界面

Element 对象中持有 `BuildOwner` 对象, 调用 `BuildOwner.scheduleBuildFor()` 设置界面为需要更新的状态




