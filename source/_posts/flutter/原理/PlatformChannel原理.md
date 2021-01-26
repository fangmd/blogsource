---
title: PlatformChannel原理(github)
date: 2020-03-21 13:18:12
tags: [Flutter]
category: Flutter

---

# Dart 侧相关

## 3种 PlatformChannel

Flutter 提供了三种 Platform Channel 用于支持 Flutter 和平台之间传递数据.(`lib/src/services/platform_channel.dart` )

1. `BasicMessageChannel<T>`: 不常用，作用发送数据和接收数据
2. `MethodChannel`(`OptionalMethodChannel`): 常用，通过和平台约定函数名，达到交互效果。
3. `EventChannel`: 常用，获取来自平台的数据流

这 3 种 PlatformChannel 的 API 设计都是类似的:

1. 需要的成员变量: name(Channel 唯一标示), codec(消息编解码), BinaryMessenger(二进制消息传送工具)
2. 发送消息和接收消息相关API

**异同点:**

1. BasicMessageChannel 不常用，MethodChannel，EventChannel 常用。
2. MethodChannel 和 EventChannel 应用场景完全不一样。一个应用于获取一个数据的场景，一个应用于获取数据流的应用场景。
3. 发送消息都是异步函数使用 `async` 修饰

## Codec 消息编解码

有两种: `MethodCodec`, `MessageCodec`

```
lib/src/services/message_codecs.dart

- abstract MessageCodec
    - StringCodec
    - BinaryCodec
    - JSONMessageCodec
    - StandardMessageCodec

- abstract MethodCodec
    - StandardMethodCodec
    - JSONMethodCodec
```

编码后生成二进制对象: `ByteData`

### 编解码核心

1. writeValue: 写的时候先写数据类型，然后写入数据
2. readValue

数据类型是提前约定的:(也就是说 Flutter 和平台交互支持下面这几种类型)

```Dart
  static const int _valueNull = 0;
  static const int _valueTrue = 1;
  static const int _valueFalse = 2;
  static const int _valueInt32 = 3;
  static const int _valueInt64 = 4;
  static const int _valueLargeInt = 5;
  static const int _valueFloat64 = 6;
  static const int _valueString = 7;
  static const int _valueUint8List = 8;
  static const int _valueInt32List = 9;
  static const int _valueInt64List = 10;
  static const int _valueFloat64List = 11;
  static const int _valueList = 12;
  static const int _valueMap = 13;
```

## BinaryMessenger 消息传递工具

作用：把二进制消息传递给平台，并从平台获取二进制消息返回给 Flutter

定义: `lib/src/services/binary_messenger.dart`

具体实现: `lib/src/services/binding.dart` 下的 `_DefaultBinaryMessenger`

最总发送 ByteData 代码:

```
    ui.window.sendPlatformMessage(channel, message, (ByteData reply) {
      try {
        completer.complete(reply);
      } catch (exception, stack) {
        FlutterError.reportError(FlutterErrorDetails(
          exception: exception,
          stack: stack,
          library: 'services library',
          context: ErrorDescription('during a platform message response callback'),
        ));
      }
    });
```

`ui.window` 属于 `sky_engine` 的类:

```Dart
window:

  void sendPlatformMessage(String name,
                           ByteData data,
                           PlatformMessageResponseCallback callback) {
    final String error =
        _sendPlatformMessage(name, _zonedPlatformMessageResponseCallback(callback), data);
    if (error != null)
      throw Exception(error);
  }
  String _sendPlatformMessage(String name,
                              PlatformMessageResponseCallback callback,
                              ByteData data) native 'Window_sendPlatformMessage';
```

>到此 Flutter SDK 层结束

**总结**:

1. Flutter 和 平台交互有3种 PlatformChannel, 支持数据和数据流
2. Flutter 和 平台交互的数据是 二进制 数据，约定了数据格式

# Android Java 侧

## Dart 到 Java 流程

Dart 侧将二进制数据传递给 Native 层，Native 层将数据通过 `FlutterJNI.java` 传递到 Java 层。

`io.flutter.embedding.engine.FlutterJNI.java`: 接收二进制

```Java
    private void handlePlatformMessage(@NonNull String channel, byte[] message, int replyId) {
        if (this.platformMessageHandler != null) {
            this.platformMessageHandler.handleMessageFromDart(channel, message, replyId);
        }

    }
```

`io.flutter.embedding.engine.dart.DartMessenger.java` `handleMessageFromDart` 函数中根据 Channel Name 查找对应的 `BinaryMessageHandler` 执行 `handler.onMessage(...)`， 最终我们自己写的 `MethodCallHandler` 实现类就会执行。




参考:

- [深入Flutter技术内幕:Platform Channel设计与实现(一)
Floating Cat
深入Flutter技术内幕:Platform Channel设计与实现(一)](http://lionoggo.com/2019/02/09/%E6%B7%B1%E5%85%A5Flutter%E6%8A%80%E6%9C%AF%E5%86%85%E5%B9%95_Platform%20Channel%E5%8E%9F%E7%90%86/)