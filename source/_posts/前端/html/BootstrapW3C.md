---
title: Bootstrap w3c 学习记录
date: 2016-11-23 13:18:12
tags: [html, 学习记录, Bootstrap]
category: 前端

---


[w3c Bootstrap](http://www.w3cschool.cn/bootstrap/bootstrap-v2-grid-system.html)
[Bootstrap中文网](http://v3.bootcss.com/css/)


# Bootstrap css


## 概览

### HTML 5 文档类型（Doctype）

    <!DOCTYPE html>
    <html>
    ....
    </html>

### 移动设备优先

head 中添加：

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

- width 属性控制设备的宽度，设置为 device-width 可以确保它能正确呈现在不同设备上。
- initial-scale=1.0 确保网页加载时，以 1:1 的比例呈现，不会有任何的缩放。
- 移动设备浏览器上，通过为 viewport meta 标签添加 user-scalable=no 可以禁用其缩放（zooming）功能。
- maximum-scale=1.0 与 user-scalable=no 一起使用。这样禁用缩放功能后，用户只能滚动屏幕，就能让您的网站看上去更像原生应用的感觉。

        <meta name="viewport" content="width=device-width,                                                           initial-scale=1.0,                                       
        maximum-scale=1.0,                                       
        user-scalable=no">

### 响应式图像

    <img src="..." class="img-responsive" alt="响应式图像">

- img-responsive class 可以让 Bootstrap 3 中的图像对响应式布局的支持更友好。


### 全局显示、排版和链接

`bootstrap.css` 文件中查看css

基本的全局显示：

    body {
      font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
      font-size: 14px;
      line-height: 1.42857143;
      color: #333;
      background-color: #fff;
    }

### 容器（Container）

    <div class="container">
      ...
    </div>

用于包裹页面上的内容:

    .container {
      padding-right: 15px;
      padding-left: 15px;
      margin-right: auto;
      margin-left: auto;
    }

# Bootstrap 网格系统

系统会自动分为最多12列。

## Bootstrap 网格系统（Grid System）的工作原理:

网格系统通过一系列包含内容的行和列来创建页面布局。

1. 行必须放置在 .container class 内，以便获得适当的对齐（alignment）和内边距（padding）。
2. 使用行来创建列的水平组。
3. 内容应该放置在列内，且唯有列可以是行的直接子元素。
4. 预定义的网格类，比如 .row 和 .col-xs-4，可用于快速创建网格布局。LESS 混合类可用于更多语义布局。
5. 列通过内边距（padding）来创建列内容之间的间隙。该内边距是通过 .rows 上的外边距（margin）取负，表示第一列和最后一列的行偏移。
6. 网格系统是通过指定您想要横跨的十二个可用的列来创建的。例如，要创建三个相等的列，则使用三个 .col-xs-4。


## 基本的网格结构

    <div class="container">
       <div class="row">
          <div class="col-*-*"></div>
          <div class="col-*-*"></div>      
       </div>
       <div class="row">...</div></div><div class="container">....

### 列偏移

`.col-md-offset-*` 类可以将列向右侧偏移

### 嵌套列

### 列排序

使用 `.col-md-push-*` 和 `.col-md-pull-*` 类就可以很容易的改变列（column）的顺序

## Less mixin 和变量

# 排版

## 标题

文字标题: <h1~h6>

副标题: <small>

段落：<p>

引导主体副本:class="lead" (给段落添加)

## 内联文本元素

1. <mark>:Marked text
2. <del>:被删除的文本
3. <s>:无用文本
4. <ins>:插入文本
5. <u>:带下划线的文本
6. <small>:小号文本
7. <strong>:（设置文本为更粗的文本）
8.  <em>（设置文本为斜体）
## 对齐

class="text-left"，class="text-center"，class="text-right"，class="text-justify"，class="text-nowrap"
## 改变大小写

class="text-lowercase"，class="text-uppercase"，class="text-capitalize"

## 缩略语

<abbr title="World Wide Web">WWW</abbr>， class="initialism" 缩小字体

强调:


6. <p class="text-muted">本行内容是减弱的</p>
7. <p class="text-primary">本行内容带有一个 primary class</p>
8. <p class="text-success">本行内容带有一个 success class</p>
9. <p class="text-info">本行内容带有一个 info class</p>
10. <p class="text-warning">本行内容带有一个 warning class</p>
11. <p class="text-danger">本行内容带有一个 danger class</p>


## 地址（Address）

<address>

## 引用（Blockquote）

<blockquote>

## 其他

- .lead	使段落突出显示
- .text-left	设定文本左对齐
- .text-center	设定文本居中对齐
- .text-right	设定文本右对齐
- .text-justify	设定文本对齐,段落中超出屏幕部分文字自动换行
- .text-nowrap	段落中超出屏幕部分不换行
- .text-lowercase	设定文本小写
- .text-uppercase	设定文本大写
- .text-capitalize	设定单词首字母大写
- .initialism	显示在 <abbr> 元素中的文本以小号字体展示
- .blockquote-reverse	设定引用右对齐
- .list-unstyled	移除默认的列表样式，列表项中左对齐 ( <ul> 和 <ol> 中)。 这个类仅适用于直接子列表项    (如果需要移除嵌套的列表项，你需要在嵌套的列表中使用该样式)
- .list-inline	将所有列表项放置同一行
- .dl-horizontal	该类设置了浮动和偏移，应用于 <dl> 元素和 <dt> 元素中，具体实现可以查看实例
- .pre-scrollable	使 <pre> 元素可滚动 scrollable

## 列表

1. class .list-inline 把所有的列表项放在同一行中

# 表格

为任意 <table> 标签添加 .table 类可以为其赋予基本的样式

example:

    <table class="table">
        <caption>Optional table caption.</caption>
        <thead>
        <tr>
            <th>#</th>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Username</th>
        </tr>
        </thead>

        <tbody>
        <tr>
            <td>1</td>
            <td>Mark</td>
            <td>Otto</td>
            <td>@mdo</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Jacob</td>
            <td>Thornton</td>
            <td>@fat</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Larry</td>
            <td>the Bird</td>
            <td>@twitter</td>
        </tr>
        </tbody>

    </table>

## 条纹状表格

通过 .table-striped 类可以给 <tbody> 之内的每一行增加斑马条纹样式。

    <table class="table table-striped">
      ...
    </table>

## 带边框的表格

# 表单

## 基本实例

    <form role="form">
      <div class="form-group">
        <label for="exampleInputEmail1">Email address</label>
        <input type="email" class="form-control" id="exampleInputEmail1" placeholder="Enter email">
      </div>
      <div class="form-group">
        <label for="exampleInputPassword1">Password</label>
        <input type="password" class="form-control" id="exampleInputPassword1" placeholder="Password">
      </div>
      <div class="form-group">
        <label for="exampleInputFile">File input</label>
        <input type="file" id="exampleInputFile">
        <p class="help-block">Example block-level help text here.</p>
      </div>
      <div class="checkbox">
        <label>
          <input type="checkbox"> Check me out
        </label>
      </div>
      <button type="submit" class="btn btn-default">Submit</button>
    </form>

## 内连表单

表单添加下面的 class 属性

- .form-inline

注意：label 一定要添加，否则屏幕阅读器无法正确识别

## 水平排列的表单

label 和 input 在一行上

- .form-horizontal 配合 栅格类
