---
title: 弹窗
date: 2017-06-13 13:18:12
tags: ios
category: ios

---


2017/6/13

参考：[http://www.jianshu.com/p/63eb8fad9329](http://www.jianshu.com/p/63eb8fad9329)

<!--more-->

# 代码

## UIAlertView

## UIActionSheet

在屏幕底部的弹窗，可以设置标题、按钮文字及个数。

## UIAlertController

iOS8推出的新controller，替代了UIAlertView和UIActionSheet，但UIAlertView和UIActionSheet仍可以使用。




# ios 弹窗设计

参考：[http://www.jianshu.com/p/63eb8fad9329](http://www.jianshu.com/p/63eb8fad9329)

## 通用提示

不具有交互功能，只是用于提示用户一些信息

会自动消息

## 警示框

需要用户进行交互的弹窗，必须保证用户确认的内容

### 两个按钮的警告框

设计：

- 如果不会造成损害性结果，取消在左，确定在右
- 如果会造成损害性结果，又是用户最容易接触的操作，例如删除软件，删除在左，取消在


## 操作列表

需要选择的一项的弹窗，比如选择日期


## 全屏模态

## 弹窗式引导页：必须包含退出按钮
