---
title: Flutter 生命周期
date: 2020-02-22 13:18:12
tags: [Flutter]
category: Flutter

---

# 监听 App 回到后台和重新打开

WidgetsBindingObserver

```Dart
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    //此处可以拓展 是不是从前台回到后台
    if (state == AppLifecycleState.resumed) {
      //on resume
      if (NavigatorManger().isTopPage(this)) {
        onForeground();
        onResume();
      }
    } else if (state == AppLifecycleState.paused) {
      //on pause
      if (NavigatorManger().isTopPage(this)) {
        onBackground();
        onPause();
      }
    }
    super.didChangeAppLifecycleState(state);
  }
```