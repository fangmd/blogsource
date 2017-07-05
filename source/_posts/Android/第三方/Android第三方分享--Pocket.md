---
title: Android第三方分享--Pocket
date: 2016-04-24 11:38:14
tags: [Pocket, 第三方]
categories: android

---

# Android第三方分享--Pocket
全过程记录

官方文档地址：[https://getpocket.com/developer/docs/authentication](https://getpocket.com/developer/docs/authentication)

## 0 注册账号，创建一个应用

[http://getpocket.com/developer/apps/new](http://getpocket.com/developer/apps/new)

结果：

![](http://7xread.com1.z0.glb.clouddn.com/3ad58dbf-29af-4451-98e5-b8c300e1b120)

## 1 获取Token

获取Token需要发起一个post请求

地址：

- https://getpocket.com/v3/oauth/request

参数：

- consumer_key-->string	创建应用后获取的key
- redirect_uri	当授权过程已完成要调用的URL。此URL应直接返回到您的应用程序。请参阅有关在iOS和Android的REDIRECT_URI设置自定义的URL的详细信息特定于平台的注释部分。
- state	string	这个参数是可选项，通过您的应用程序使用的元数据的字符串。该字符串将在所有后续的认证响应返回。

<!--more-->

## 2 用户授权请求

### 方法一

If you are on iOS and are able to detect the presence of the URL scheme pocket-oauth-v1, you should redirect the user to the Pocket app like this:

pocket-oauth-v1:///authorize?request_token=YOUR_REQUEST_TOKEN&redirect_uri=YOUR_REDIRECT_URI

Example using above:
pocket-oauth-v1:///authorize?request_token=dcba4321-dcba-4321-dcba-4321dc&redirect_uri=pocketapp1234:authorizationFinished 


### 方法二

If you are on any other platform -or- are not able to detect the URL scheme, you redirect the user to the Pocket web site like this:

https://getpocket.com/auth/authorize?request_token=YOUR_REQUEST_TOKEN&redirect_uri=YOUR_REDIRECT_URI
Example using above:
https://getpocket.com/auth/authorize?request_token=dcba4321-dcba-4321-dcba-4321dc&redirect_uri=pocketapp1234:authorizationFinished

## 3 接收Pocket的反馈

用户在Pocket界面授权或者拒接授权后，Pocket应用会打开请求时的参数`redirect_uri `让用户返回原应用

## 4 转变  request token 为 access token

当应用接收到Pocket的反馈的时候，可以通过UI表明应用获取了权限，并且发起一个Post请求：

地址：

- https://getpocket.com/v3/oauth/authorize

参数：

- consumer_key	string	第一步时使用的key
- code	string	第二步中post请求使用的request_token code

这个post请求放回的结果是：access_token，username

**到此为止：**获取应用授权就结束了，之后可以使用consumer_key，access_token直接连接Pocket进行数据处理。

## 5 发送分享请求到Pocket

需要参数：

- consumer_key
- access_token

一个请求例子(JSON)：

	POST /v3/add HTTP/1.1
	Host: getpocket.com
	Content-Type: application/json; charset=UTF-8
	X-Accept: application/json
	
	{"url":"http:\/\/pocket.co\/s8Kga",
	"title":"iTeaching: The New Pedagogy (How the iPad is Inspiring Better Ways of 
	Teaching)",
	"time":1346976937,
	"consumer_key":"1234-abcd1234abcd1234abcd1234",
	"access_token":"5678defg-5678-defg-5678-defg56"}

结果Example response (JSON):

	HTTP/1.1 200 OK
	Content-Type: application/json
	Status: 200 OK
	
	{"status":1}



