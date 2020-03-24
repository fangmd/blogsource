---
title: Flutter sdk概述
date: 2020-03-23 13:18:12
tags: [Flutter]
category: Flutter

---

Flutter SDK 分几大模块:

1. `animation`
2. `cupertino`, `material`
3. `foundation`
4. `gestures`
5. `painting`
6. `physics`
7. `rendering`
8. `scheduler`
9. `semantics`
10. `services`
11. `widgets`


## gestures

手势处理相关

`/Users/double/flutter/sdk/flutter/packages/flutter/lib/src/gestures/binding.dart`

入口: `GestureBinding` 获取来自 engine 的触摸数据

设置回调获取触摸数据: `window.onPointerDataPacket`

## services

实现 Flutter 和 平台数据交互的功能。

binding 入口: `ServicesBinding`

设置回调获取平台数据: `Window.onPlatformMessage`

```Dart
  @override
  void initInstances() {
    super.initInstances();
    _instance = this;
    _defaultBinaryMessenger = createBinaryMessenger();
    window
      ..onPlatformMessage = defaultBinaryMessenger.handlePlatformMessage;
    initLicenses();
    SystemChannels.system.setMessageHandler(handleSystemMessage);
  }
```

`defaultBinaryMessenger.handlePlatformMessage` 回调拿到数据后，进行解析，根据 Channel Name 找到对应 PlatformChannel 进行处理数据。

## scheduler

调度器，管理页面帧

binding 入口: `SchedulerBinding`

设置回调, 是懒设置的:

```Dart
  @protected
  void ensureFrameCallbacksRegistered() {
    window.onBeginFrame ??= _handleBeginFrame;
    window.onDrawFrame ??= _handleDrawFrame;
  }
```

会调用 `Window.scheduleFrame`, 提示 engine 渲染 frame.

## painting

## semantics

## rendering

binding 入口: `RendererBinding`

设置回调:

```Dart
    window
      ..onMetricsChanged = handleMetricsChanged
      ..onTextScaleFactorChanged = handleTextScaleFactorChanged
      ..onPlatformBrightnessChanged = handlePlatformBrightnessChanged
      ..onSemanticsEnabledChanged = _handleSemanticsEnabledChanged
      ..onSemanticsAction = _handleSemanticsAction;
```

调用: `_window.render(scene)`

## widgets

binding 入口: `WidgetsBinding`






























