---
title: HTML
date: 2016-03-25 21:18:12
tags: HTML
categories: java基础
---

## HTML
### 什么是HTML
html是用来描述网页的一种语言。

- html指的是超文本标记语言(Hyper Text Markup Language)
- HTML不是一种编程语言，而是一种标记语言(markup language)
- 标记语言是一套**标记标签**(markup tag)
- HTML使用**标记标签**来描述网页

特点：

1. 通过游览器来解释执行
2. 弱类型的语言(语言不严谨)
3. 标签不具备扩展性，标签的含义固定死了
4. 标签都是成对出现，允许嵌套，但不允许交叉嵌套
5. 大小写不铭感
6. 后缀是.html或者是.htm
7. 一个html源文件由三部分：声明部分、头部分、正文部分
	- 声明部分：标识了dtd文档，约定源程序中有哪些标签，含义何在
	- 头部：html附加的信息说明，会对正文部分进行约束
	- 正文部分：被游览器解析的内容

<!--more-->

### HTML标签
HTML标记标签通常被称为HTML标签(HTML tag).

- HTML标签是由**尖括号**包围的关键词，比如<html>
- HTML标签通常是**成对出现**，比如：<b></b>
- 标签对中的第一个标签是**开始标签**，第二个标签是**结束标签**
- 开始和结束标签也被称为**开放标签**和闭合标签

### HTML文档 与 网页
- HTML文档描述网页
- HTML文档包含HTML标签和纯文本
- HTML文档也被称为网页

web游览器的作用是读取HTML文档，并以网页的形式显示出它们。游览器不会显示标签，而是用标签来解释页面的内容

### 基本标签
#### html标题
	<h1> -- <h6> 数组越小，字号越大
	<h1>this is a heading</h1>

#### html段落
	<p>this is a paragraph.</p>
	<p>this is a another paragraph</p>
#### html 链接
	通过<a>标签定义
	<a href="http://www.baidu.com">this is a link</a>
	href：属性中指定链接地址，点击“this is a link”跳转
#### html 图像
	<img src="地址" width="104" height="142" />
	图像的名称和尺寸是以属性的形式提供的。
## 必须知道标签
### html页面主体结构
	<html>	html页面开始
		<head>	页面头信息，不会显示页面只，设置标题
		</head>

		<body>	网页的主体内容，页面显示的部分
		</body>
	</html>	html页面结束
### input 标签
	<!-- input标签，根据type属性值得不同，被游览器可以解析为不同的控件 1. text：默认值，文本输入框； 2. password：密码输入框； 3. radio：单选按钮； 4. checkbox：复选框 -->

	<p>
		用户名：<input type="text" name="name" />
	</p>
	<p>
		密&nbsp;&nbsp;码：<input type="password" name="pwd" />
	</p>
	<p>
	    <!-- 此处name的属性值必须相同，表示下述两个单选项是同一组-->
		性&nbsp;&nbsp;别：<input type="radio" name="gender" />男
		                  <input type="radio" name="gender" />女
	</p>
	<p>
		爱&nbsp;&nbsp;好：<input type="checkbox" name="hobby" />看电影
		                  <input type="checkbox" name="hobby" />聊天
		                  <input type="checkbox" name="hobby" />运动
	</p>
### ul li 标签
	ul:无序列表标签；li:列表项
	<ul>
		<li>Dreamweaver</li>
		<li>Photoshop</li>
		<li>Firwork</li>
	</ul>
### ol 标签
	ol:有序列表标签 li：列表项
	<ol>
		<li>Dreamweaver</li>
		<li>Photoshop</li>
		<li>Firwork</li>
	</ol>
### select 标签
	select：下拉列表框标签
	<p>
		你的籍贯是：<select>
			<option>北京</option>
			<option>上海</option>
			<option>广州</option>
			<option>深圳</option>
		</select>省
	</p>
### form 表单
	form表单：用来封装数据，将子标签中的数据封装成一个整体，向目的页面提交过去。
	action属性：表示表单提交目的页面的地址
	method属性：表单提交的方式(get，post)
	
	get:默认的，效果：在网址栏上能够看到提交的数据-->不安全数据的长度<=1k
	post:效果：在网址栏看不到提交的数据-->安全，数据的长度<=2G

	注意：触发表表单向目的页面跳转，需要有一个“按钮”，类型为：submit的input标签

	<p>
		<input type="submit"  value="注册"/>
	</p>