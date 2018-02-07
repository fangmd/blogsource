---
title: Android测试 - Monkey压力测试
date: 2017-03-15 13:18:12
tags: [压力测试, android]
category: android

---


基本语法

```
adb shell monkey [options] <event-count>
```

# 开始一个随机测试

```
adb shell monkey -p [package name] -v [event-count]

adb shell monkey -p com.nerc.communityedupro -v 2000
```

- `-p` 后面跟 包名，表示需要测试的应用
- `-v` 表示显示测试事件
- `2000` 表示测试事件的数量 （越大，测试过程越久，测试程度越高）


# 如何出现问题后如何复现

```
-s <seed>: Seed value for pseudo-random number generator. If you re-run the Monkey with the same seed value, it will generate the same sequence of events.
```	

使用 `-s` 命令给本次测试命名，相同的 seed 名称对应相同的测试过程。

>注意 seed 名称必须是数字

```
adb shell monkey -s 1 -p com.nerc.communityedupro 100
```

在没有设置 seed 的情况下，在测试过程用应用 creash 后，monkey 会返回一个 seed。**(所以在测试的时候通常不需要测试这个值)**

复现问题的时候需要将应用的初始状态设置到一样，最好是直接后台关闭。


# 减慢测试事件的速度

```
--throttle <milliseconds>: Inserts a fixed delay between events. You can use this option to slow down the Monkey. If not specified, there is no delay and the events are generated as rapidly as possible.
```

```
adb shell monkey --throttle 300 -p com.nerc.communityedupro 100
```


# 使用脚本测试固定事件

## monkeyrunner UI 测试脚本

[官方文档](https://developer.android.com/studio/test/monkeyrunner/index.html)



参考：

- [https://developer.android.com/studio/test/monkey.html](https://developer.android.com/studio/test/monkey.html)
- [https://developer.android.com/training/testing/start/index.html](https://developer.android.com/training/testing/start/index.html)
- [http://www.vogella.com/tutorials/AndroidTesting/article.html](http://www.vogella.com/tutorials/AndroidTesting/article.html)
- [http://blog.csdn.net/jieqiang3/article/details/53928638](http://blog.csdn.net/jieqiang3/article/details/53928638)


