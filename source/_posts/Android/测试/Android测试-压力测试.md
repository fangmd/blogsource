---
title: Android测试-压力测试
date: 2017-03-15 13:18:12
tags: [压力测试, android]
category: android

---

# Using the Monkey tool for creating a random event stream

## How to use Monkey

```
adb shell monkey -p [package name] -v 2000
```
如果出现问题：重启 adb
```
adb kill-server
adb start-server
```


# monkeyrunner UI 测试脚本

![官方文档](https://developer.android.com/studio/test/monkeyrunner/index.html)




参考：

- [https://developer.android.com/training/testing/start/index.html](https://developer.android.com/training/testing/start/index.html)
- [http://www.vogella.com/tutorials/AndroidTesting/article.html](http://www.vogella.com/tutorials/AndroidTesting/article.html)


