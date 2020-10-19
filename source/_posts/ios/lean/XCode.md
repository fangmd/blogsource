---
title: XCode 相关
date: 2017-05-19 13:18:12
tags: ios
category: ios

---

# 个人体验

1. 慢：报错慢，提示慢
2. 错误提示体验很差
3. 没有 AS 中的点语法 “.var .field”
4. 没有 AS 中的模版语法
5. 代码重构不方便


<!--more-->


# 常用快捷键

- 格式化代码： `control + i`
- 快捷修复代码：
- 清理工程缓存：`CMD + shift + k`  (在 pod install 后如果第三方包 import 失败可以先清理一下工程)
- 清理：`command + shift + option + k`

# Group

在工程中创建 Group，会发现在 finder 中工程的文件并没有按照 group 被分配。

group：只是在 xcode 中管理了文件，并没有改变文件的实际位置


# Simulator 

[缩放 Simulator 让整个屏幕显示出来](https://stackoverflow.com/questions/25915915/unable-to-show-full-iphone-6-plus-simulator-window-on-non-retina-display)

```
Command Key + 1 : 100%
Command Key + 2 : 75%
Command Key + 3 : 50%
Command Key + 4 : 33%
Command Key + 5 : 25%
```

# 注释

```
// MARK: <label name>
// TODO: <text you want to remember>
// FIXME: <text you want to remember>
```

方法注释(光标放在方法上一行)：`option + command + /`