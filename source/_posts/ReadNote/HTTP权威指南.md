---
title: HTTP 权威指南
date: 2016-09-16 13:18:12
tags: [HTTP]
category: 读书笔记

---

# HTTP Web的基础

## HTTP 概述

### HTTP －因特网的多媒体信使
HTTP使用的是可靠的数据传输协议，能确保数据在传输的时候不会损坏或者产生混乱

### Web客户端和服务器

服务器也称为HTTP服务器，Web客户端向服务器发送HTTP请求，服务器返回数据。

### 资源

Web服务器是Web资源（Web resource）的宿主。Web资源是Web内容的源头。

最简单的Web资源就是Web服务器上的静态文件，这些文件可以是任意格式的。

#### 媒体类型

根据不同的数据类型，HTTP给每个要通过Web传输的对象都打上MIME type的数据格式标签。

MiME type：描述被标记的多媒体内容。是一种文本标记，中间使用一条斜杠来分隔

几个例子

- 文本文档 text/html
- ASCII文本文档 text/plain
- JPEG image/jpeg
- GIF image/gif
- QuickTime video/quicktime
- PowerPoint application/vnd.ms-powerpoint

<!--more-->
#### URI

Uniform Resource Identifier 统一资源标识符

Web服务器上面资源的地址

使用URI反问Web服务器上的某个资源

有两种形式

- URL
- URN

#### URL

统一资源定位符

描述服务器上

包含三个部分：

- scheme 方案：说明访问资源使用的协议类型，通常是http://
- 服务器的ip或者域名地址， 比如 www.baidu.com
- 剩余部分是Web服务器上的某个资源 比如 /specials/saw.gif


现在几乎说有的URI都是URL

#### URN

统一资源名

与资源的所在地无关，资源可以随地迁移，但是访问的地址不变

    urn:ietf:rfc:2141

### 事务

客户端如何通过HTTP与Web服务器及其资源进行事务处理？

一个HTTP事务包含：

1. 请求命令
2. 响应结果

这种通信通过：HTTP message （HTTP 报文）

#### 方法
HTTP支持不同的请求命令，hTTP method

|HTTP method|description|
|-----------|--------------|
|get|从服务器向客户端发送命名资源|
|put|将来自客户端的数据存储到一个命名的服务器资源中|
|delete|从服务器删除命名资源|
|post|将客户端数据发骚女到服务器网关应用程序|
|head|仅发送命名资源相应中的HTTP头部|

#### 状态码

HTTP响应报文会返回一个状态码

三位数字的代码

还会伴随原因短语：

    200 OK
    200 Document attached
    200 Success
    200 All's cool, dude

#### Web页面包含多个对象

一个Web页面通常会发起多个HTTP请求

### 报文

报文：

- request message
- response message


message contains:

1. 起始行: 请求报文例子：`GET /tools.html HTTP/1.0` response:`HTTP/1.0 200 OK`
2. 首部字段：请求头，可有可无，键值对形势，中间用`:`号分隔
3. 主体：请求体，头部＋空行后就是请求体，可以是文本也可以是二进制.

简单报文实例

### 连接

Transmission Control Protocol TCP

报文通过传输控制协议连接

#### TCP/IP

HTTP是应用层协议，无需关心网络通信细节

因特网传输协议：

1. 无差错的数据传输
2. 按序传输
3. 未分段的数据流


5层结构：

1. HTTP 应用层
2. TCP 传输层
3. IP 网络层
4. 网络特有的链路接口 数据链路层
5. 物理网络硬件 物理层

#### 连接，ip地址几端口 

连接需要：ip／域名，端口（没有指定端口号的时候默认80）

通过域名服务 Domain Name Service DNS 将ip转化成域名

有了IP和端口就能通过TCP／IP协议通信了。

#### 使用Telnet实例

### 协议版本

HTTP／1.0

HTTP－NG（HTTP/2.0）

### Web的结构组件

几个重要的应用程序：

1. 代理：位于客户端和服务器之间的HTTP中间实体
2. 缓存：HTTP仓库，将常用页面副本保存在里客户端近的地方
3. 网关：连接其他应用程序的特殊Web服务器
4. 隧道：对HTTP通信报文进行盲转发的特殊代理
5. Agent代理：自动发起HTTP请求的半智能Web客户端

#### 代理

代表客户端访问服务器

可以对请求和响应进行过滤

#### 缓存

Web cache，proxy cache

#### 网关
gateway

作为其他服务器的中间实体使用，通常用于HTTP流量转换成其他协议。

#### 隧道
tunnel

#### Agent代理

Web游览器就是一种Agent代理
