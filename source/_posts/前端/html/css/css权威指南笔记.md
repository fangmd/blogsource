---
title: html5 权威指南笔记
date: 2018-05-07 13:18:12
tags: [html]
category: 前端

---

# HTML 标记与文档结构

1. 文本用闭合标签
2. 引用内容用自闭合标签

```
块级标签：
<h1>：6级标签
<p>：段落
<ol>：有序列表
<li>：列表项
<blockquote>: 独立引用

行内标签：
<a>: 连接
<img>: 图片
<em>: 斜体
<strong>: 重要
<abbr>: 简写
<cite>: 引证
<q>: 文本内引用
```

### HTML模版

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8"/>
        <title>MyTitle</title>
        <style>
        /* CSS 规则放在这里*/
        </style>
    </head>

    <body>
        <!-- 网页内容 -->

    </body>

</html>
```

# CSS 工作原理

给文档添加样式的三种方法：

1. 行内样式
2. 嵌入样式
3. 链接样式 `<link href="styles.css" rel="stylesheet" type="text/css" />`

CSS 规则命名惯例：

```
p{color:red;}
选择符号{属性: 值;}
```

选择符组合：

```
h1, h2, h3{color: blue; font-weight: bold;}
```

多条规则作用于一个选择符号：

```
h1, h2, h3{color: blue; font-weight: bold;}
h3{font-style: italic;}
```

选择符分类：

1. 上下文选择符
2. ID和类选择符号
3. 属性选择符

## 上下文选择符

```
article p {font-weight: bold;}
```

子选择符`>` (父子元素)

```
section > h2 {font-style: italic;}
```

紧邻同胞选择符号+: (同胞且相邻)

```
h2 + p {font-variant: smaill-caps;}
```

一般同胞选择符:

```
h2 ~ a {color: red;}
```

通用选择符:

```
* {color: green;}
```

## ID 和 类选择符

```
.类名
```

标签带类选择符：

```
p.specialtext {color: red;}
```

多类选择器：(同时设置多个类的标签)

```
.specialtest.featured{font-size: 120%}
```

ID 选择符号：`#`

```
<p id="specialtext"> This is the special text.</p>

#specialtext {...}
```

ID 可以用于页内导航

```
<a href='#bio'> Biographt </a>

返回顶部：

<a href='#'> Top </a>
```

### ID 与 类 区别

ID 页内唯一。

类：页面中一类标签。

## 属性选择符

选择任何带有某个属性名的标签。

```
标签名[属性名]

标签名[属性名="属性值"]
```

### UI 伪类

```
link: 链接等待点击
visited: 点击过的链接
hover: 鼠标悬停
active: 链接正在被点击

a:hover {color: red}
```

## 继承

CSS 中的祖先 元素也会向后代传递一样东西：CSS 属性的值。

不是所有的属性都能继承。

# 定位元素

## 盒模型

每个元素在页面上都会生成一个盒子。

盒子属性：border, padding, margin

```
{margin: 5px 10px 12px 8px;}
```

### 叠加外边距

```
p {height:50px; border:1px solid #000; backgroundcolor:#fff; margin-top:50px; margin-bottom:30px;}
```

两个 p 标签相遇的时候，之间的距离只有 50, 而不是 80.

## 盒子大小

## 定位

css 布局的核心 `position` 属性：static, relative, absolute, fixed. 默认值：static.

## 显示属性

display: block, inline

## 背景

```
background-color
background-image
background-repeat
background-position
background-size
background-attachment
```

背景图居中：

```
background-image:url(images/turq_spiral_150.png);
background-repeat: no-repeat;
background-position: 50% 50%;
```

背景尺寸：

```
// fit xy
background-size: cover;

// 缩放图片，使其恰好适合背景区；保持宽高比
background-size: contain;
```

背景粘附：背景图片是否随元素滚动

```
background-attachment: fixed;
```

简写背景属性:

```
body{
    background: url(images/watermark.png) center #fff no-repeat contain fixed;
}
```

# 字体和文本

## 字体

```
font-family
font-size
font-style
font-weitht
font-variant
font (简写属性)
```

```
p{
    font: blod italic small-caps .9em helvetica, arial, sans-serif;
}
```

简写规则：

1. 必须声明 font-size, font-family
2. 顺序：`font-weight, font-syle, font-variant` 不分先后，然后是 `font-szie`, `font-family`

## 文本属性

```
text-indent: 文本缩进
letter-spacing: 字符间距
word-spacing: 单词间距
text-decoratioin: 文本装饰 underline, overline, line-through, blink, none
text-align: 文本对齐 left right center justify
line-height: 行高
text-transform: 文本转换 none, uppercase, lowercase, capitalize
vertical-align: 垂直对齐 sub, super, top, middle, bottom, 长度值
```

# 5 页面布局












