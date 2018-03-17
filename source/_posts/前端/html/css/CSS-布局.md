---
title: CSS 布局
date: 2018-03-14 13:18:12
tags: [css]
category: 前端

---

# display 属性

display 是 CSS 中最重要的用于控制布局的属性。每个元素都有一个默认的 display 值，这与元素的类型有关。

对于大多数元素它们的默认值通常是 block 或 inline 。一个 block 元素通常被叫做块级元素。一个 inline 元素通常被叫做行内元素。

## block

一个块级元素会新开始一行并且尽可能撑满容器。

常见块级元素：`div`, `form`, `p`, html5 中的 `header`, `footer`, `section`

## inline

插入后不会打破原有的布局。

常见: `span`，`a`

## none

隐藏一个空间，类似 Android 中 visibile 属性中的 none。


# margin: 0 auto;

```
#main {
  width: 600px;
  margin: 0 auto; 
}
```

设置一个元素的宽度，然后设置 `margin: 0 auto`, 使其相对父控件 **水平居中**。

`max-width: 600px;`: 约束实现适配窗口小于设置的 width 的问题。

# position

## static

元素不会被特殊的定位

>section 的 position 默认是 static

## relative

表现的和 static 一样，除非你添加了一些额外的属性.

`top`, `left`, `bottom`, `right`: 表示相对父控件的位置坐标。

```
.relative1 {
  position: relative;
}
.relative2 {
  position: relative;
  top: -20px;
  left: 20px;
  background-color: white;
  width: 500px;
}
```

## fixed

元素会相对于视窗来定位，

`top`, `left`, `bottom`, `right`: 表示相对 窗口 的位置坐标。

## absolute

跳过父控件，直接相对 body 元素进行定位。

`top`, `left`, `bottom`, `right`: 表示相对 body 控件的位置坐标。

# float 

实现文字环绕图片效果。

# flexbox

Flex 容器（flex container）

6 个属性作用于容器：

- flex-direction:  row | row-reverse | column | column-reverse, 决定主轴方向（项目排列方向）
- flex-wrap：nowrap | wrap | wrap-reverse， 项目排列不下的时候是否要换行
- flex-flow：`<flex-direction> || <flex-wrap>`
- justify-content: flex-start | flex-end | center | space-between | space-around; 项目在主轴上的排列方式
- align-items：flex-start | flex-end | center | baseline | stretch; 项目在交叉轴上如何排列
- align-content: 决定行之间的间隔
- flex: flex-grow flex-shrink flex-basis|auto|initial|inherit;

作用于子控件：

- align-self: flex-start | flex-end | center | baseline | stretch, 子控件在交叉轴上如何排列




# 例子

## 左导航，右数据列表

![layout-blog](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/css/layout-blog.png)

```

.layout-container {
    height:100%;
    width: 100%;
    position: relative;
}

.layout-left  {
    background-color: red;
    width: 20%;
    height: 100%;
    position: fixed;
}

.layout-right {
    background-color: blue;
    width: 80%;
    margin-left: 20%;
    position: static;
}

.layout-right h1{
    height: 200px;
}

.layout-right-list-container{
    position: relative;
    margin: 0 auto;
    max-width: 970px;
    background-color: green;
}
```

```
            <div class="layout-container">
                
                <div class="layout-left">
                    <h1>Title</h1>
                </div>
                <div class="layout-right">

                    <div class="layout-right-list-container">

                        <h1>Title</h1>
                        <h1>Title</h1>
                        <h1>Title</h1>
                        <h1>Title</h1>
                        <h1>Title</h1>
                        <h1>Title</h1>

                    </div>

                </div>      

            </div>
```

## flex 实现 左导航，右数据列表

```
.layout-container {
    display: -webkit-flex;
    display: flex;
    width: 100%;
    height: 100%;
}

.layout-left {
    width: 20%;
    height: 100%;
    background-color: red;
    position: fixed;
}

.layout-right {
    display: flex;
    width: 80%; 
    background-color: blue;
    margin-left: 20%;
}

.layout-right h1 {
    height: 200px;
}

.layout-right-list-container {
    max-width: 970px;
    height: 100%;
    margin: 0 auto;
    background-color: green;
}
```



参考：

- [http://zh.learnlayout.com/](http://zh.learnlayout.com/margin-auto.html)
- [http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
- [http://flexboxfroggy.com/#zh-cn](http://flexboxfroggy.com/#zh-cn)
