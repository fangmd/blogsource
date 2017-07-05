---
title: HTTP 
date: 2016-08-28 13:18:12
tags: [HTTP]
category: ReadNote

---

原文：[http://www.kancloud.cn/kancloud/tealeaf-http/43832](http://www.kancloud.cn/kancloud/tealeaf-http/43832)

## 背景

HTTP 解释：

1. 规则系统，协议
2. 把应用程序和超文本文档之间的传输联系起来
3. 是机器之间彼此沟通的协议，或者说消息格式
4. 遵循一个简单的模型：请求－－响应

历史：

1. 1991年 HTTP／0.9
2. 1992年 HTTP／1.0
3. 1995年 HTTP／1.1

## 请求包

![请求包](http://upload-images.jianshu.io/upload_images/680540-e04227416a611216.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

### 无状态
HTTP是无状态的，服务器不需要在每次请求之间保留状态信息，每次请求都是独立的

## 什么是URL
Uniform Resource Locator 统一资源标识符

### URL 组成部分
- `http`：url的模式scheme，还有ftp，mailto，git
- `www.example.com`:资源路径或者主机，告诉客户端资源的确切位置
- `/home/`url路径，代表客户端在请求什么样的本地资源（对于服务器来说）

80端口默认用于正常的HTTP请求

### 查询字符串／参数

    http://www.example.com?search=ruby&results=10

|查询字符串|描述|
-------------------
|？|保留字，标识查询字符串的开始|
|search＝ruby|参数的键值对|
|&|保留字，给需要查询字符串添加参数|

限制：

1. 查询字符串有最大长度。所以，如果你大量的数据需要传输，还是不要用查询字符串的好。
2. 查询字符串中使用的键/值对儿是显示在 URL 上的。所以，不推荐用查询字符串传输敏感信息比如用户名或密码。
3. 查询字符串中无法使用空格和特殊字符比如&。它们必须用 URL 编码代替，我们接下来会讨论这个。

### URL 编码
URL在设计的时候默认只接受ASCLL码

## HTTP

### HTTP请求报文

#### 请求行 Request Line

    GET /entry/57bbdeb4df0eea005c630f45/view HTTP/1.1

- Method
- url
- version


#### 请求头

#### 请求体

### 发起一个请求

http工具推荐：chrome插件：postman，

使用http工具发起请求，查看返回的数据RAW

## 处理响应

### 状态码
### 头部
### 消息正文

## 有状态的WEB应用

- session会话
- Cookies
- 异步javascript调用（AJAX）

### session
服务器在发送响应数据的时候带一个唯一令牌token，之后客户端在发起请求的时候都把token加在后面

### Cookies
存储在游览器里包含会话信息的小文件。

### AJAX

## 安全性

### HTTPS
`https://`
通过 HTTPS 发送的请求和响应在发送前都会被加密。这意味着如果一个恶意的黑客监听 HTTP 通信，他得到的信息都是加密的和无用的。

TLS加密协议

### 同源策略 Same-origin policy

