---
title: Flutter 启动流程
date: 2020-03-23 13:18:12
tags: [Flutter]
category: Flutter

---


**Dart 端入口 runApp**

```Dart
void runApp(Widget app) {
  WidgetsFlutterBinding.ensureInitialized()
    ..scheduleAttachRootWidget(app)
    ..scheduleWarmUpFrame();
}
```

`WidgetsFlutterBinding.ensureInitialized`:

- `WidgetsFlutterBinding` 是一个单例(懒汉式)
- `ensureInitialized` 静态方法，初始化单例对象，初始化各种 `xxxBinding`
- `RendererBinding` 初始化的时候会创建 `RenderView` 并设置成需要绘制状态

`..scheduleAttachRootWidget(app)`:

- 使用 `RenderObjectToWidgetAdapter` 将 `Widget` 转化成 `RenderObjectToWidgetElement`
- `RenderObjectToWidgetElement` 管理所有的 `RenderObject`

`..scheduleWarmUpFrame();`:

调度第一帧画面。

2个帧由 engine 调起的回调: `window.onBeginFrame`, `window.onDrawFrame`


## 帧渲染 frame rendered

- 帧(frame) 添加到 调度器(scheduler) 中，并且两个回调已经注册(`window.onBeginFrame`, `window.onDrawFrame`), 引擎(engine) 开始自动请求帧
- `RenderView.compositeFrame`  调用 `_window.render(scene);` 渲染 帧(frame)

通过 RenderView 向 engine 传递界面信息。

## Flutter 框架层 和 引擎层如何交互 (framework interact with the engine)

- 框架层和引擎层通过 `Widnow` 累交互。
- 框架层通过注册各种 callback，从引擎层获取数据
- 每个 binding 服务在框架层中都是一个单例。




参考：

- [https://www.flutterinternals.org/core/framework](https://www.flutterinternals.org/core/framework)






