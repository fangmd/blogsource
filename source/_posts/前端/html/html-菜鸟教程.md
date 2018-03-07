---
title: html-w3school 教程
date: 2016-04-02 21:38:14
tags: [html,菜鸟教程]
categories: android

---

[w3school](http://w3school.com.cn/html/html_intro.asp)

# 什么是html

用来描述网页的语音。

- HTML指的是超文本标记语言(Hyper Text Markup Language)
- HTML不是一种编程语言，而是一种标记语言(markup language)
- 标记语言是一套标记标签(markup tag)
- HTML使用标记标签来描述网页

# HTML标签
- 由尖括号包围的关键词
- 成对出现
- 开始标签和结束标签
- 开始和结束也称为开放标签和闭合标签

<!--more-->

# HTML文档 = 网页
Web游览器作用是读取HTML文档，并以网页的形式显示他们。

# 基本HTML标签 Tag
- 标题：`<h1>-<h6>`
- 段落：`<p>`
- 链接：`<a>`,在 href 属性中指定链接的地址
- 图像：`<img>`,在属性中指定图像的名称，尺寸
- <html> 与 </html> 之间的文本描述网页
- <body> 与 </body> 之间的文本是可见的页面内容


# HTML元素 Elememt

## 什么是HTML元素

开始标签和结束标签之间的所有内容，包含标签；
## HTML元素语法
## 嵌套HTML元素
## 空的HTML元素
>没有内容的元素，空元素在开始标签中关闭；

- `<br/>`：换行，自关闭

>使用小写标签

# HTML属性
以键值对形式存在：`name = “value”`

## 属性实例
- `align="center"`：元素内容对齐方式，居中排列标题
- `bgcolor="yellow"`:background color,<body>
- `border="1"`:表格边框的附加信息,<table>

属性加添加眼号，如果属性中自带双眼号，外面要添加单眼号

# HTML 标题

- <h1> - <h6>

**注意：**不要为了粗体使用标题标签

## HTML 水平线

- <hr />

可以用来分割文章的小节

## HTML 注释

    <!-- This is a comment -->

# HTML 段落

浏览器会自动地在段落的前后添加空行。（<p> 是块级元素）

**注意：**不要使用 <p></p> 去插入空行，因该使用 <br />

## HTML 折行

不产生一个新段落的情况下进行换行（新行），请使用 <br />

当显示页面时，浏览器会移除源代码中多余的空格和空行。所有连续的空格或空行都会被算作一个空格。需要注意的是，HTML 代码中的所有连续的空行（换行）也被显示为一个空格。

# HTML 样式

- style 属性:提供了一种改变所有 HTML 元素的样式的通用方法。

HTML 4 引入

不赞成使用的标签和属性：

标签：
- <center>
- <font> 和 <basefont>
- <s> 和 <strike>
- <u>

属性：

- align
- bgcolor
- color

## style：背景颜色

- style="background-color:yellow"

## style 字体、颜色和尺寸

font-family、color 以及 font-size

    <html>

    <body>
    <h1 style="font-family:verdana">A heading</h1>
    <p style="font-family:arial;color:red;font-size:20px;">A paragraph.</p>
    </body>

    </html>

##  style  文本对齐

    <body>
    <h1 style="text-align:center">This is a heading</h1>
    <p>The heading above is aligned to the center of this page.</p>
    </body>

# HTML 文本格式

文本格式化标签:

- <b>	定义粗体文本。
- <big>	定义大号字。
- <em>	定义着重文字。
- <i>	定义斜体字。
- <small>	定义小号字。
- <strong>	定义加重语气。
- <sub>	定义下标字。
- <sup>	定义上标字。
- <ins>	定义插入字。
- <del>	定义删除字。
- <s>	不赞成使用。使用 <del> 代替。
- <strike>	不赞成使用。使用 <del> 代替。
- <u>	不赞成使用。使用样式（style）代替。

“计算机输出”标签:

- <code>	定义计算机代码。
- <kbd>	定义键盘码。
- <samp>	定义计算机代码样本。
- <tt>	定义打字机代码。
- <var>	定义变量。
- <pre>	定义预格式文本。
- <listing>	不赞成使用。使用 <pre> 代替。
- <plaintext>	不赞成使用。使用 <pre> 代替。
- <xmp>	不赞成使用。使用 <pre> 代替。

引用、引用和术语定义:

- <abbr>	定义缩写。
- <acronym>	定义首字母缩写。
- <address>	定义地址。
- <bdo>	定义文字方向。
- <blockquote>	定义长的引用。
- <q>	定义短的引用语。
- <cite>	定义引用、引证。
- <dfn>	定义一个定义项目。

# HTML 引用

## <q> 用于短的引用


## <blockquote> 长引用

## <abbr> 缩略词

对缩写进行标记能够为浏览器、翻译系统以及搜索引擎提供有用的信息。

    <abbr title="World Health Organization">WHO</abbr>

## <dfn> 元素定义项目或缩写的定义


## <address> 联系信息

## <cite> 著作标题

##  <bdo> 双向重写

# HTML 计算机代码元素

- <code>	定义计算机代码文本
- <kbd>	定义键盘文本
- <samp>	定义计算机代码示例
- <var>	定义变量
- <pre>	定义预格式化文本

# 注释

    <!-- Write your comments here -->   

注释里面可以嵌套标签

条件注释：

    <!--[if IE 8]>
        .... some HTML here ....
    <![endif]-->

# CSS

- <style>	定义样式定义。
- <link>	定义资源引用。
- <div>	定义文档中的节或区域（块级）。
- <span>	定义文档中的行内的小块或区域。
- <font>	规定文本的字体、字体尺寸、字体颜色。不赞成使用。请使用样式。
- <basefont>	定义基准字体。不赞成使用。请使用样式。
- <center>	对文本进行水平居中。不赞成使用。请使用样式。

## 如何使用样式

当浏览器读到一个样式表，它就会按照这个样式表来对文档进行格式化。

### 外部样式表

当样式需要被应用到很多页面的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观。


    <head>
    <link rel="stylesheet" type="text/css" href="mystyle.css">
    </head>

### 内部样式表

    <head>

    <style type="text/css">
    body {background-color: red}
    p {margin-left: 20px}
    </style>
    </head>

### 内联样式

    <p style="color: red; margin-left: 20px">
    This is a paragraph
    </p>

# 链接
- <a>	定义锚。

## 超链接（链接）

超链接可以是一个字，一个词，或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。

有两种使用 <a> 标签的方式：

1. 通过使用 href 属性 - 创建指向另一个文档的链接
2. 通过使用 name 属性 - 创建文档内的书签

##  target 属性

- _blank:打开新窗口

定义被链接的文档在何处显示。

## name 属性

# 图像

- <img>	定义图像。
- <map>	定义图像地图。
- <area>	定义图像地图中的可点击区域。

## 图像标签（<img>）和源属性（Src）

    <img src="url" />

## 替换文本属性（Alt）

alt 属性用来为图像定义一串预备的可替换的文本。替换文本属性的值是用户定义的。

## 图片作为背景图片

    <body background="/i/eg_background.jpg">    

如果图片不够大，就会重复

## 设置图片大小

- height
- width

## 把图像转换为图像映射

    <a href="/example/html/html_ismap.html">
    <img src="/i/eg_planets.jpg" ismap />
    </a>

# 表格


- <table>	定义表格
- <caption>	定义表格标题。
- <th>	定义表格的表头。
- <tr>	定义表格的行。
- <td>	定义表格单元。
- <thead>	定义表格的页眉。
- <tbody>	定义表格的主体。
- <tfoot>	定义表格的页脚。
- <col>	定义用于表格列的属性。
- <colgroup>	定义表格列的组。

属性：

- colspan=“2”：横跨两列
- cellpadding=“10”：设置内边距
- cellspacing=“10”：设置外边距

## 表格和边框属性
- border

## 表格的表头

- <th>

大多数浏览器会把表头显示为粗体居中的文本

## 表格中的空单元格

建议添加 &nbsp; 作为内容占位

    <td>&nbsp;</td>

# 列表

- <ol>	定义有序列表。
- <ul>	定义无序列表。
- <li>	定义列表项。
- <dl>	定义定义列表。
- <dt>	定义定义项目。
- <dd>	定义定义的描述。
- <dir>	已废弃。使用 <ul> 代替它。
- <menu>	已废弃。使用 <ul> 代替它。

# 块

>“块级元素”译为 block level element，“内联元素”译为 inline element。

##  块元素

块级元素在浏览器显示时，通常会以新行来开始（和结束）。

## 内联元素

内联元素在显示时通常不会以新行开始。


##  <div> 元素

如果与 CSS 一同使用，<div> 元素可用于对大的内容块设置样式属性。

## <span> 元素

当与 CSS 一同使用时，<span> 元素可用于为部分文本设置样式属性。



- <div>	定义文档中的分区或节（division/section）。
- <span>	定义 span，用来组合文档中的行内元素。


# 类

对 HTML 进行分类（设置类），使我们能够为元素的类定义 CSS 样式。


1. 使用 div 分类块级元素，设置 class

2. 使用 span 分类行内元素，设置 class

# 布局

- header	定义文档或节的页眉
- nav	定义导航链接的容器
- section	定义文档中的节
- article	定义独立的自包含文章
- aside	定义内容之外的内容（比如侧栏）
- footer	定义文档或节的页脚
- details	定义额外的细节
- summary	定义 details 元素的标题

# 响应式 Web 设计

- RWD 指的是响应式 Web 设计（Responsive Web Design）
- RWD 能够以可变尺寸传递网页
- RWD 对于平板和移动设备是必需的

# 框架

通过使用框架，你可以在同一个浏览器窗口中显示不止一个页面。每份HTML文档称为一个框架，并且每个框架都独立于其他的框架。

坏处：

1. 开发人员必须同时跟踪更多的HTML文档
2. 很难打印整张页面

框架结构标签（<frameset>）：

1. 框架结构标签（<frameset>）定义如何将窗口分割为框架
2. 每个 frameset 定义了一系列行或列
3. rows/columns 的值规定了每行或每列占据屏幕的面积

# Iframe 内联框架

- <iframe>	定义内联的子窗口（框架）

# 背景

# 脚本

- <script>	定义客户端脚本。
- <noscript>	为不支持客户端脚本的浏览器定义替代内容。

# 头部元素

<head> 内的元素可包含脚本，指示浏览器在何处可以找到样式表，提供元信息，等等

以下标签都可以添加到 head 部分：<title>、<base>、<link>、<meta>、<script> 以及 <style>。

- <head>	定义关于文档的信息。
- <title>	定义文档标题。


    - 定义浏览器工具栏中的标题
    - 提供页面被添加到收藏夹时显示的标题
    - 显示在搜索引擎结果中的页面标题
- <base>	定义页面上所有链接的默认地址或默认目标。
- <link>	定义文档与外部资源之间的关系。

    定义文档与外部资源之间的关系。
- <meta>	定义关于 HTML 文档的元数据。

    提供关于 HTML 文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。

- <script>	定义客户端脚本。
- <style>	定义文档的样式信息。
    用于为 HTML 文档定义样式信息。

# 字符实体

显示结果	描述	实体名称	实体编号

- 空格	&nbsp;	&#160;
- <	小于号	&lt;	&#60;
- >	大于号	&gt;	&#62;
- &	和号	&amp;	&#38;
- "	引号	&quot;	&#34;
- '	撇号 	&apos; (IE不支持)	&#39;
- ￠	分	&cent;	&#162;
- £	镑	&pound;	&#163;
- ¥	日圆	&yen;	&#165;
- €	欧元	&euro;	&#8364;
- §	小节	&sect;	&#167;
- ©	版权	&copy;	&#169;
- ®	注册商标	&reg;	&#174;
- ™	商标	&trade;	&#8482;
- ×	乘号	&times;	&#215;
- ÷	除号	&divide;	&#247;

# 文档类型

<!DOCTYPE>

    <!DOCTYPE html>
