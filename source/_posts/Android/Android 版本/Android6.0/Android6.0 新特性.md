---
title: Android6.0 新特性
date: 2016-02-04 22:38:14
tags: [Android6.0]
categories: Android

---


# 运行时权限

- checkSelfPermission()
- requestPermissions()

# 低电耗模式和应用待机模式

- 低电耗模式：如果用户拔下设备的电源插头，并在屏幕关闭后的一段时间内使其保持不活动状态，设备会进入低电耗模式，在该模式下设备会尝试让系统保持休眠状态。在该模式下，设备会定期短时间恢复正常工作，以便进行应用同步，还可让系统执行任何挂起的操作。
- 应用待机模式：应用待机模式允许系统判定应用在用户未主动使用它时处于空闲状态。当用户有一段时间未触摸应用时，系统便会作出此判定。如果拔下了设备电源插头，系统会为其视为空闲的应用停用网络访问以及暂停同步和作业

# 取消支持 Apache HTTP 客户端

开启方式：

```
android {
    useLibrary 'org.apache.http.legacy'
}
```

# BoringSSL

# 硬件标识符访问权

对于使用 WLAN API 和 Bluetooth API 的应用，Android 移除了对设备本地硬件标识符的编程访问权。WifiInfo.getMacAddress() 方法和 BluetoothAdapter.getAddress() 方法现在会返回常量值 02:00:00:00:00:00。

现在，要通过蓝牙和 WLAN 扫描访问附近外部设备的硬件标识符，您的应用必须拥有 ACCESS_FINE_LOCATION 或 ACCESS_COARSE_LOCATION 权限。

- WifiManager.getScanResults()
- BluetoothDevice.ACTION_FOUND
- BluetoothLeScanner.startScan()

# 通知

改用 Notification.Builder 类来构建通知。要重复更新通知，请重复使用 Notification.Builder 实例。调用 build() 方法可获取更新后的 Notification 实例。

# 音频管理器变更

不再支持通过 AudioManager 类直接设置音量或将特定音频流静音。setStreamSolo() 方法已弃用，您应该改为调用 requestAudioFocus() 方法。类似地，setStreamMute() 方法也已弃用，请改为调用 adjustStreamVolume() 方法并传入方向值 ADJUST_MUTE 或 ADJUST_UNMUTE。

# 文本选择

# 相机服务变更

 机服务中共享资源的访问模式已从之前的“先到先得”访问模式更改为高优先级进程优先的访问模式。

 - 根据客户端应用进程的“优先级”授予对相机子系统资源的访问权，包括打开和配置相机设备。带有对用户可见 Activity 或前台 Activity 的应用进程一般会被授予较高的优先级，从而使相机资源的获取和使用更加可靠；
 - 当高优先级的应用尝试使用相机时，系统可能会“驱逐”正在使用相机客户端的低优先级应用。在已弃用的 Camera API 中，这会导致系统为被驱逐的客户端调用 onError()。在 Camera2 API 中，这会导致系统为被驱逐的客户端调用 onDisconnected()；
 





