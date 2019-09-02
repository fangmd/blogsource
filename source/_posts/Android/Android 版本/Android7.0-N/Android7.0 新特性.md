---
title: Android7.0 新特性
date: 2016-02-04 22:38:14
tags: [Android7.0]
categories: Android

---


# 新的应用签名方案

APK Signature Scheme v2

http://tech.meituan.com/android-apk-v2-signature-scheme.html


目前该方案不是强制性的，在 build.gradle 添加 v2SigningEnabled false ，就能使用传统签名方案来签署我们的应用：

```
  android {
    ...
    defaultConfig { ... }
    signingConfigs {
      release {
        storeFile file("myreleasekey.keystore")
        storePassword "password"
        keyAlias "MyReleaseKey"
        keyPassword "password"
        v2SigningEnabled false
      }
    }
  }
```

# 多窗口支持

# 通知功能增强

- 模板更新：我们正在更新通知模板，新强调了英雄形象和化身。 开发者将能够充分利用新模板，只需进行少量的代码调整。
- 消息样式自定义：您可以自定义更多与您的使用 MessageStyle
类的通知相关的用户界面标签。 您可以配置消息、会话标题和内容视图。
- 捆绑通知：系统可以将消息组合在一起（例如，按消息主题）并显示组。 用户可以适当地进行 Dismiss 或 Archive 等操作。 如果您已实现 Android Wear 的通知，那么您已经很熟悉此模型。
- 直接回复：对于实时通信应用，Android 系统支持内联回复，以便用户可以直接在通知界面中快速回复短信。
- 自定义视图：两个新的 API 让您在通知中使用自定义视图时可以充分利用系统装饰元素，如通知标题和操作。


# 低电耗模式

# Project Svelte：后台优化

# 来电过滤
Android N 允许默认的手机应用过滤来电。手机应用执行此操作的方式是实现新的 CallScreeningService，该方法允许手机应用基于来电的 [Call.Details](https://developer.android.com/reference/android/telecom/Call.Details.html)
执行大量操作，例如：

- 拒绝来电
- 不允许来电到达通话记录
- 不向用户显示来电通知

# 直接启动



# 作用域目录访问

# VR（虚拟现实）支持

参考：

- [http://www.jianshu.com/p/2cdd40417650](http://www.jianshu.com/p/2cdd40417650
- [https://github.com/googlesamples/android-ScopedDirectoryAccess](https://github.com/googlesamples/android-ScopedDirectoryAccess)