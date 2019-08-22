---
title: CSS 知识点集合
date: 2019-08-20 13:18:12
tags: [css]
category: 前端

---

摘录自:[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS)

CSS 层叠样式表，用于描述 HTML 文档的呈现。

# CSS 如何工作的 

![https://mdn.mozillademos.org/files/11781/rendering.svg](https://mdn.mozillademos.org/files/11781/rendering.svg)

1. 游览器将 HTML CSS 转化成 DOM(文档对象模型)
2. 游览器显示 DOM

>DOM:在计算机内存中表示文档。将文档内容和样式结合在一起

## DOM

DOM 是树形结构，html 中的每个元素属性文本都变成一个 DOM 节点。

# CSS 引入 HTML 

1. 外部样式

`<link rel="stylesheet" href="style.css">`

2. 内部样式

```css
    <style>
      h1 {
        color: blue;
        background-color: yellow;
        border: 1px solid black;
      }
    </style>
```

3. 内联样式

`<h1 style="color: blue;background-color: yellow;border: 1px solid black;">Hello World!</h1>`

>内联样式不易维护，尽量不要使用

# 基础

CSS组成: 

1. 选择器 Selector
2. 属性 Properties
3. 属性值 Property Value
4. 声明 Declaration = 属性 + 属性值

CSS 结构称为：规则集(规则)

## 选择器

- 元素选择器: `p`
- ID 选择器: `#my-id`
- 类选择器: `.my-class`
- 属性选择器: `img[src]`, 支持3种情况: 包含某个属性，包含属性并属性是指定值，只包含某个属性并是指定值
- 伪类选择器: `a:hover`: 常用伪类: `active, only-child, checked, hover, first, focus, visited, enabled, read-only, disabled, invalid, valid`
- 伪元素: 给元素添加元素, 常用: `::after, before`
- 组合器
- 多重选择器
- 通用选择器: `*`

多元素选择器: 使用逗号分割 `p,li`

## CSS 语句

倒入其他 css

```css
@import 'custom.css';
```

嵌套语句:

```css
@media (min-width: 801px) {
  body {
    margin: 0 auto;
    width: 800px;
  }
}
```

# 属性

简写属性

```
font
background
padding
border
margin
```