---
title: Flutter App 禁止横屏显示
date: 2020-10-19 13:18:12
tags: [Flutter]
category: Flutter

---

# Android

`AndroidManifest.xml`， `.MainActivity` 加属性:

```
android:screenOrientation="portrait"
```

# iOS 

在 xcode 中修改即可