---
title: ADB 命令记录
date: 2016-08-07 13:18:12
tags: [ADB, shell]
category: Android

---


# ADB shell 记录

## dumpsys power

查看当前电源使用，
Wake Locks: size=0：查看当前持有 wake locak的应用

## /system/framework/ 中的文件修改权限

设置为读写

    # mount -o rw,remount /system

设置为读（默认）

    # mount -o ro,remount /system

# ADB 使用例子

## 查看当前 Activity

```
adb shell dumpsys window windows | grep -E 'mCurrentFocus|mFocusedApp'
```