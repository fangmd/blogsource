---
title: macOS 创建定时任务 
date: 2016-12-25 12:18:12
tags: [Mac OS]
category: Mac OS

---

# Mac 上执行定时任务 

有两种方式

1. 使用 `launchctl`
2. 使用 Linux 中常用的 `crontab` 命令

# launchctl

## 创建任务
launchctl 任务以文件形式创建，后缀为 `plist`

### 文件格式

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">  
<plist version="1.0">  
  <dict>
    <!-- 名称，要全局唯一 -->
    <key>Label</key>
    <string>com.double.notifier</string>

    <!-- 要运行的程序， 如果省略这个选项，会把ProgramArguments的第一个
    元素作为要运行的程序 -->
    <key>Program</key>
    <string>/Users/double/script.sh</string>

    <!-- 命令， 第一个为命令，其它为参数-->
    <key>ProgramArguments</key>
    <array>
      <string>/Users/double/script.sh</string>
    </array>

    <!-- 运行时间 -->
    <key>StartCalendarInterval</key>
    <dict>

      <key>Minute</key>
      <integer>30</integer>

      <key>Hour</key>
      <integer>9</integer>

      <key>Day</key>
      <integer>1</integer>

      <key>Month</key>
      <integer>5</integer>

      <!-- 0和7都指星期天 -->
      <key>Weekday</key>
      <integer>0</integer>

    </dict>

    <!-- 运行间隔，与StartCalenderInterval使用其一，单位为秒 -->
    <key>StartInterval</key>
    <integer>30</integer>

    <!-- 标准输入文件 -->
    <key>StandardInPath</key>
    <string>/Users/uniflor/run-in.log</string>

    <!-- 标准输出文件 -->
    <key>StandardOutPath</key>
    <string>/Users/uniflor/Bin/run-out.log</string>

    <!-- 标准错误输出文件 -->
    <key>StandardErrorPath</key>
    <string>/Users/uniflor/Bin/run-err.log</string>

  </dict>  
</plist>
```

文件解析：

- Label
- Program
- ProgramArguments

定义时间的参数：

- StartCalendarInterval
- StartInterval

可选输出参数：

- StandardInPath
- StandardOutPath
- StandardErrorPath

### 检查任务文件的格式

```
plutil -line [file.plist]
```

### 任务文件的放置位置

通常放在 `~/Library/LaunchAgents` 下即可

- ~/Library/LaunchAgents 由用户自己定义的任务项
- /Library/LaunchAgents 由管理员为用户定义的任务项
- /Library/LaunchDaemons 由管理员定义的守护进程任务项
- /System/Library/LaunchAgents 由Mac OS X为用户定义的任务项
- /System/Library/LaunchDaemons 由Mac OS X定义的守护进程任务项

#### LaunchDaemons和LaunchAgents的区别

LaunchDaemons是用户未登陆前就启动的服务（守护进程）。

LaunchAgents是用户登陆后启动的服务（守护进程）。

### 加载任务

添加任务

```
launchctl load [file.plist]
```

移除任务
```
launchctl unload [file.plist]
```

查看任务
```
launchctl list
```

参考：

- [https://my.oschina.net/shede333/blog/470377](https://my.oschina.net/shede333/blog/470377)