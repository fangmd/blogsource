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
- 组合器: 后代选择器(空格分割)，子选择器(`>`)，相邻兄弟选择器(`+`)，通用兄弟选择器(`~`)
- 多重选择器(选择器组): 使用 `,` 分割选择器
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
# 单位

长度和尺寸

px:

em: 相对于当前元素字号(`font-size`)大小

rem: (root em),相对于根元素字号

vw,vh: 视口宽高的1/100,

# 函数

- `rgba`
- `rotate`
- `calc`
- `url`

# 样式优先级

1. 重要性 `!important`

优先级最高

2. 专用性 `Specificity`

```
元素选择器: 1
类选择器，伪类，属性: 10
id 选择器: 100
内联样式: 1000
```

3. 源代码次序 `Source order`

后面的 css 覆盖前面的 css (css 解析顺序)

## css 继承

//TODO:

# 盒模型

display: block | inline | inline-block | table | flex | grid

box-sizing: content-box | border-box

```
content-box: width 只包括内容区域
border-box: width 包括 border + padding
```

# Base CSS 通常要初始化哪些内容

[normalize.css](https://necolas.github.io/normalize.css/8.0.1/normalize.css)

1. 清空控件的 外边距
2. 游览器适配处理(IE 一些表现与主流不一致的，添加与主流一致的样式: display)


# 布局

## 定位 position + left|top|bottom|right

position: static(默认) | relative | fixed | absolute

- static：元素框正常生成。块级元素生成一个矩形框，作为文档流的一部分，行内元素则会创建一个或多个行框，置于其父元素中。
- relative：元素框偏移某个距离。元素仍保持其未定位前的形状，它原本所占的空间仍保留。
- absolute： 元素框从文档流完全删除，并相对于其包含块定位。包含块可能是文档中的另一个元素或者是初始包含块。元素原先在正常文档流中所占的空间会关闭，就好像元素原来不存在一样。元素定位后生成一个块级框，而不论原来它在正常流中生成何种类型的框。
- fixed：元素框的表现类似于将 position 设置为 absolute，不过其包含块是视窗本身。


## 浮动 float

float: left | right | none | inherit

### 清除浮动

需要清除浮动的原因: float 使元素脱离文档流，不能撑开父元素.

方法一: clear 属性

```css
.clearfix{
  clear: both;
}
```

方法二: 伪元素 + clear

```css
.clearfix:after{
  content: '';
  display: 'block';
  clear:both;
}
```

方法三: overflow

```css
.clearfix{
  overflow: hidden;
}
```


## flex

display: flex

基本概念：

1. 主轴 main axis
2. 交叉轴 cross axis

```
flex-direction: 指定主轴, column | row | row-reverse | column-reverse
flex-wrap: 换行

尺寸：
flex: 1; 比例 (flex-grow)
flex: 1 200px; 设置最小值 (flex-grow flex-shrink)

缩写
flex-flow: flex-direction flex-wrap
flex: flex-grow flex-shrink flex-basis

控制轴上元素排列方式
align-items: 交叉轴 stretch(default) | center | flex-start | flex-end
justify-content: 主轴 flex-start(default) | flex-end | center | space-around | space-between
```

## 适配

不适配:

IE: <=9

# BFC Bloack Format Context

普通文档流包括：块级盒子的块级格式化上下文，内联级盒子的格式化上下文，块级和内联级盒子的相对定位。

常见格式化上下文：

1. Black formatting context, BFC
2. Inline formatting context, IFC
3. Grid formatting context GFC
4. Flex formatting context, FFC

什么情况会创建BFC:

1. 根元素 html, 或其他包含它的元素
2. 浮动元素(float)
3. 绝对定位元素(position:absolute, fixed)
4. 非块级盒子的块级容器(display: inline-blocks, table-cells, table-captions 等)
5. overflow 不为 visiable 的块级盒子(oveflow: hidden, auto, scroll)

>一个元素不能同时存在两个BFC中

BFC 特性:

1. 盒子占一行，盒子从顶部开始垂直排列
2. 两个相邻的盒子之间垂直距离，由margin决定
3. 块级格式化上下文中: 相邻的盒子之间的垂直距离边距折叠(相邻的margin重叠)
4. FBC 高度计算，浮动子元素也参数计算


## 应用场景

### 关与margin，重叠

```html
例子:

<body>
  <div class="one">
  <div>
  <div class="two">
  <div>
</body>

one, two maring 会重叠，因为在同一个 BFC 下(html 下)

处理方式: 将 one，two 放在不通的 BFC 环境下

<body>
  <div class="container">
    <div class="two">
    <div>
  <div>
  <div class="container">
    <div class="one">
    <div>
  <div>
</body>

需要给 container 创建 BFC, (overflow: hidden;)
```

### BFC 可以包含浮动元素

清除浮动的时候使用 BFC (overflow: hidden)


### BFC 阻止元素被浮动元素覆盖

实现两列自适应布局。左边固定宽度，右边内容自适应

```
<div>左浮动元素</div>
<div>没有设置浮动，但是触发 BFC(overflow:hidden) 避免被左浮动元素文字环绕</div>
```

# CSS3 & CSS 需要知道的点

1. CSS3 完全兼容 CSS
2. CSS3 添加了圆角属性 `roundBorder`
3. CSS3 对添加了更多的文字属性: 换行 `word-break`; 超出省略号 `text-overflow: ellipsis`; 文字阴影 `text-shadow`;
4. CSS3 更好的动画实现: 过渡 `transition`, 动画 `animation`, 形状转换 `transform`, 
5. CSS3 添加更多的选择器，常用: `:nth-child(n); nth-last-child(); :last-child; :enabled; :disabled; :checked; ::selection`
6. CSS3 添加了阴影属性 `box-shadow`, 边框属性 `border-image`, 背景裁切 `background-clip`, 背景大小 `background-size`
7. CSS3 颜色 `rgba`
7. CSS3 添加了媒体查询
8. CSS3 添加了弹性布局 Flex，栅格布局 Grid，多列布局 
9. CSS3 盒模型 `box-sizing: border-box | content-box`




