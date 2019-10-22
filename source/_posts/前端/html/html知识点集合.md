---
title: Html 知识点集合
date: 2019-08-01 13:18:12
tags: [html]
category: 前端

---


载录自：[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML)

HTML: 超文本标记语言 HyperText Markup Language, 定义网页内容的含义和结构

# 基本知识点

1. 超文本：网页与网页之前的跳转链接
2. 标记(markup): 例子 head, title, body
3. 标签(tag): `<>`  不区分大小写

# 元素

1. 块级元素
2. 内联元素
3. 空元素
4. 嵌套元素

## 属性

1. 元素可以拥有属性：比如 class, id
2. 布尔属性: 可以没有值, `<input type="text" disabled>, <input type="text" disabled="disabled">`
3. 属性在不加眼号的时候也是可以的，但是在某些情况下会影响原来的语意，所以始终添加眼号可以避免不必要的问题，同时增加可读性
4. 单眼号和双眼号都可以，但是必须成对使用

# 分析一个 HTML 文档

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>

<body>

</body>

</html>
```

1. `<!DOCTYPE html>`: 声明文档类型

早期的 HTML 文档类型声明类似与链接，链接里面定义了页面需要遵循的规则

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

2. `<html>` 根元素
3. `<head>`: 存放包含在 html 中，但是不会显示到界面上的内容
4. `<meta charset="UTF-8">`: 文档字符集编码
5. `<title>`
6. `<body>`: 包含访问的页面上所显示的内容

## html

1. 为文档设置主语言 `<html lang="en-US">`

方便搜索引擎，允许在特定语言搜索结果中显示


## head

存放包含在 html 中，但是不会显示到界面上的内容

1. `<title>`
2. `<meta>`: 元数据，可以指定字符集，利用 name, content 特性 优化 SEO

优化 SEO，在 `<meta name="description" content="The Mozil" >`, description 会在搜索引擎中显示。

> meta, name=keyword 已经不再使用，因为作弊者太多

3. `<link rel="shortcut icon" href="favicon.ico" type="image/x-icon">` 自定义图标

16*16 像素，ico 格式(大多数游览器也支持 gif,png; ico 可以适配 ie6)

4. css 引入 `<link rel="stylesheet" href="my-css-file.css">`

5. js 引入 `<script src="my-js-file.js"></script>`

>script 元素不是空元素，必须有结束标记

# HTML 特殊字体

如果需要在界面上显示 `< > " ' &` 字符，需要用 html 规定的等价字符代替。

# 链接

`<a href="https://www.mozilla.org/en-US/">the Mozilla homepage</a>.`

1. 可以添加 `title` 属性设置，当鼠标悬停的时候，会出现的提示信息
2. 下载链接时，使用 `download` 属性, 表示下载文件，而不是使用游览器打开文件

```
<a href="https://download.mozilla.org/?product=firefox-latest-ssl&os=win64&lang=en-US"
   download="firefox-latest-64bit-installer.exe">
  Download Latest Firefox for Windows (64-bit) (English, US)
</a>
```

## URL

URL: Uniform Resource Locator, 统一资源定位符

1. 文档片段 定位, `href="contacts.html#Mailing_address"`, `#` 后面的是元素 id

# html 书写要求

1. 语义化

语义元素：

```
页面结构：
header: 页眉
nav: 导航栏
main: 主内容, 主内容中的子内容区段使用：article, section, div
aside: 侧边栏，通常嵌套在 main 中
footer: 页脚
```

无语义元素:

```
span
div
```

换行与水平风格线: `br, hr`

# 多媒体与嵌入

## 图片

`<img src="images/dinosaur.jpg">`

`src` 中能使用相对地址就使用相对地址，如果使用绝对地址会增加游览器工作(游览器需要重新从 DNS 中寻找 IP 地址)

SEO 相关：搜索引擎也会获取图片文件名称，所以一个描述性的文件名会更好

`alt` 属性，设置在图片加载不出来的时候显示的文本

>`imv`,`video` 这样的元素称为替换元素，因为这种元素内容和尺寸由外部资源决定。

### SVG

SVG: 用于描述矢量图像的XML语言

- 旧版浏览器不支持SVG，因此如果您需要在网站上支持旧版本的 IE，则可能不适合（SVG从IE9开始得到支持）。

### 响应式图片

## 音频/视频

在 HTML5 之前，html 不支持视频，都是通过 `Flash` 加载视频的。

HTML5 提供了新的媒体解决方案：`video, audio`


## 嵌入

iframe: 允许将其他 web 文档嵌入到当前文档中。

设置禁止 iframe: 请求头中加 `X-Frame-Options: deny`

### iframe 安全隐患

1. 点击劫持 (Clickjacking)

攻击者将一个或多个透明的 iframe 覆盖在一个正常的网页上，用户点击网页的时候实际上是点击了 iframe 里面的内容。

使用到的技术点：

1. 目标网页隐藏技术: css opacity 属性；双 iframe 隐藏
2. 点击操作劫持: 按钮伪造(按钮伪造成一个用户熟悉的按钮)
3. 拖拽技术: 拖拽实现跨域操作


安全隐患解决方案：

1. 禁止 iframe
2. 使用 HTTPS: HTTPS防止嵌入式内容访问您的父文档中的内容，反之亦然。
3. iframe 使用 sandbox 属性

### CSP 指令

CSP：内容安全策略, 提供一组 http 头

比如 `X-Frame-Options`, 可以防止其他网站在其网站上嵌入你的内容(Clickjacking)


# 游览器解析 HTML

游览器解析 HTML 并显示，解析的过程是很宽松的，在 HTML 中出现错误的时候，页面页能够显示。

游览器在解析 HTML 的时候会尝试修复一部分语法错误。


## HTML 语法排错

HTML 错误分析：https://validator.w3.org/


# 其他

1. HTML 中的空白

在标签内容中使用多个空白(空白字符 包括换行), 渲染代码的时候 html 会将连续空白合作为一个空白处理

# 表单

表单不能嵌套

## 常用元素

form, label, input, textarea, button

1. form 标签

属性: `action`, `method`

2. input 

是空元素

```
type: 设置类型
value: 设置默认值
name: 表示数据提交时候的 key
```

3. `<textarea>`

不是空元素

4.  button

`<button type="submit">Send your message</button>`

```
type: submit, reset, button
```

5. label

label 的 `for` 属性与 input 的 `id` 属性可以关联到一起

## 表单属性

### 通用属性

```
autofocus: default false,
disabled: default false
name: 提交时使用的 key
value: 元素初始值
```

### 文本输入框 (input, textarea)

```
readonly, disabled
placeholder
size
```

#### 单行文本框

```
type: email, text, password, search, tel, url, number(max min), range
可选中 type (额外具有 checked 属性) : checkbox, radio
日期 type: datetime-local, month, time, week, date
拾色器 type: color
文件选择 type(accept 设置文件类型): file
隐藏内容 type: hidden
```

#### 多行文本框

```
cols: default 20, 文本控件可见宽度
rows
wrap: default soft, soft|hard
```
#### 按钮

## 表单数据校验

客户端校验

1. JS 校验
2. HTML5 内置校验

服务器端校验

HTML5 内置校验属性:

```
required
pattern: 正则匹配
maxlength, minlength
自定义错误信息: addEventListener("input", function), 判断控件状态
```

约束 API `validity`

# 事件模式

事件发生时传播的过程。分为3个阶段:

1. 捕获阶段: 从 window 对象(自上向下)传到目标节点
2. 目标阶段: 在目标节点，目标接受事件
3. 冒泡阶段: 从节点(自下向上)传会到 window 对象

# 异步加载 js, async, defer

1. async: 