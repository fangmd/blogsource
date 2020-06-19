---
title: Flutter 插件相关
date: 2020-03-19 13:18:12
tags: [Flutter]
category: Flutter
---

>https://flutter.dev/docs/development/packages-and-plugins/developing-packages

# Flutter 插件引入

方式一: 从 pub 引入

```
date_utils: ^0.1.0+3
```

方式二: 从本地源文件引入

```
im_flutter_sdk:
  path: ../im_flutter_sdk
```

方式三: 从 git 地址引入

```
zegoliveroom_plugin:
  git:
    url: git://github.com/zegoim/zego-flutter-sdk.git
```

```
zegoliveroom_plugin:
  url: git://github.com/zegoim/zego-flutter-sdk.git
  ref: branch_name
```


# 如何使用 Xcode 编辑插件的 IOS 代码

1. 先将 插件 example 工程在 ios 模拟器上运行一遍
2. 使用 Android studio 打开 ios 文件夹下的 swift 文件，点击右上角 `Open iOS module in Xcode`
3. 插件 ios 代码目录在 `Pods/Developmentt Pods/插件名称/../../example/ios/.symlinks/plugins/插件名称/ios/`

# Android 获取 Application

```Kotlin
public class PInstallPlugin : FlutterPlugin, MethodCallHandler {

    private var app: Context? = null

    override fun onAttachedToEngine(@NonNull flutterPluginBinding: FlutterPlugin.FlutterPluginBinding) {
        val channel = MethodChannel(flutterPluginBinding.getFlutterEngine().getDartExecutor(), "p_install")
        val plugin = PInstallPlugin()
        plugin.app = flutterPluginBinding.applicationContext
        channel.setMethodCallHandler(plugin)
    }

    companion object {
        private const val FILE_PATH = "filePath"

        @JvmStatic
        fun registerWith(registrar: Registrar) {
            val channel = MethodChannel(registrar.messenger(), "p_install")
            val plugin = PInstallPlugin()
            plugin.app = registrar.activity().application
            channel.setMethodCallHandler(plugin)
        }
    }

    override fun onMethodCall(@NonNull call: MethodCall, @NonNull result: Result) {
        if (call.method == "install") {
        } else {
            result.notImplemented()
        }
    }

    override fun onDetachedFromEngine(@NonNull binding: FlutterPlugin.FlutterPluginBinding) {
        app = null
    }
}

```

# 插件发布

1. 去除 `pubspec.yaml` 文件中的 `author:`
2. 添加 `pubspec.yaml` `homepage: [github 地址]`
3. 发布:

> 需要翻墙(命令行设置代理)

```
flutter packages pub publish --server=https://pub.dartlang.org
```

上传成功后可以去 `https://pub.dartlang.org` 地址搜索下有没有(可能不会马上出现)

# Version not match

```
Because p_project depends on p_umeng ^0.0.3 which doesn't match any versions, version solving failed.
```

确认 `https://pub.dev/` 和 `https://pub.flutter-io.cn` 上有这个版本。

命令行开代理，手动执行:

```
flutter packages get
```


## 问题

```
pub get failed (1; Because chicha_client depends on p_install ^0.0.3 which doesn't match any versions, version
solving failed.)
```


