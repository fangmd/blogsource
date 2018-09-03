---
title: json、servlet综合案例
date: 2016-03-25 21:18:12
tags: java基础
category: java基础
---

## json、servlet综合案例


### 文件上传客户端需要使用的api
第三方jar包，涉及的api：

1. FileItemFactory:文件项工厂接口，专门用于操作FileItem
2. DefaultFileItemFactory:FileItemFactory的实现类
3. ServletFileUpload:用来进行文件上传操作
	1. ServletFileUpload(FileItemFactory):构造方法，根据参数FileItemFactory创建对象
	2. List<FileItem> parseRequest(FttpServletRequest):解析来自客户端的请求，将请求中的数据封装到List集合中，集合中每个元素的类型属于FileItem

4. FileItem:对表单中每一个项目的抽取和封装，用户名、密码、头像、xxx
	1. getName();
	2. get

<!--more-->
### 服务器
思路：

1. 获得来自客户端提交过来的数据
2. 处理数据
	1. 将用户信息存储到表中(省略)
		1. 用户表：tb_user(id,name,pwd,photo-->图片在服务端存储的路径)
	2. 将用户的头像存储到服务器的一个文件夹下


---
api总结MyRegist


### 文件上传客户端需要使用的api




### 客户端
思路：

1. 准备数据
2. HttpClient对象
3. HttpPost
4. 将数据封装在HttpPost中
4. 发送请求
5. 获得反馈，处理
6. 释放资源





