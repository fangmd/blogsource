---
title: Android Debug Bridge
date: 2016-07-02 13:18:12
tags: [adb]
category: android

---

# Android Debug Bridge (adb)
官网地址:[https://developer.android.com/studio/command-line/adb.html#forwardports](https://developer.android.com/studio/command-line/adb.html#forwardports)

*adb*是一个功能丰富的命令行工具,它让你能够和虚拟机或者Android设备交互.

是一个客户端-服务器类型的程序,包括:

1. 客户端:用来发送命令,它运行在电脑中,我们可以通过shell调用客户端.另外的Android工具比如DDMS也可以创建adb客户端.
2. 守护进程:用于在设备中运行命令.它作为一个后台进程运行在虚拟机或者Android设备中
3. 服务器:管理服务器和守护进程之间的交互.它作为后台进程运行在电脑中.

*adb*工具地址:`<ask>/platform-tools`(这个地址一般会配置环境变量)

## adb如何工作
开启*adb客户端*的时候

1. 检查是否有已经运行的*adb 服务器*. 如果没有就创建服务器.
	- 服务器创建的时候会绑定一个*TCP 端口 5037*
	- 并且监听客户端发送出来的命令(客户端发送的所有命令都经过*5037*端口到达*adb server*)
2. 服务器连接所有正在运行的虚拟机/真机.
	- 服务器通过搜索端口*5555-5585*来搜索虚拟机/真机.
	- 如果在端口中搜索到*守护进程*,就会建立到这个端口连接
	- 每一个*5555-5585*的端口对应一个设备(如果有多个设备连接)

		Emulator 1, console: 5554
		Emulator 1, adb: 5555
		Emulator 2, console: 5556
		Emulator 2, adb: 5557
		and so on...
3. 连接成功后就可以使用adb命令控制设备了

## 启用adb
1. 手机端开机*USB 调试*
2. (可选)开启*应用未知来源*
3. (可选)开启存储卡模式

## adb命令 格式

	adb [-d|-e|-s <serialNumber>] <command>

### 查询当前连接的设备

	adb devices
结果:

	[serialNumber] [state]

- Serial number:比如emulator-5554,格式`<type>-<consolePort>`
- State:offline/device/no device

### 执行命令的时候指定端口

	adb -s <serialNumber> <command>

比如:

	adb -s emulator-5556 install helloWorld.apk

### 安装应用到设备

	adb install <path_to_apk>

### 和设备文件交互

从设备中获取文件:

	adb pull <remote> <local>

发送文件到设备:

	adb push <local> <remote>

### 停止adb Server

	adb kill-server

### 无线连接设备
*(如果通过usb连接设备出现 no serialNumber 的时候可以尝试下面的连接方式)*

1. 手机和电脑在同一个wifi网下
2. 连接usb线
3. `$ adb tcpip 5555`
4. 断开usb
5. 在手机`设置-->关于-->手机状态信息-->IP地址`
6. `$ adb connect <device-ip-address>`
7. `$ adb devices`

# 使用方法

## 查看当前手机的应用

	adb shell pm list packages

## 卸载应用

	adb uninstall [package name]

## 清理 app 数据

	adb shell pm clear packageName
