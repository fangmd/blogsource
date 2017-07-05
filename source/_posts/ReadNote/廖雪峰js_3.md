---
title: 廖雪峰 js 3
date: 2016-08-14 13:18:12
tags: [js]
category: 读书笔记

---

# JQuery 

<!--more-->

丢失

## 操作DOM

jQuery对象，不需要考虑游览器的差异，全部统一操作。

### 修改Text和HTML
jQuery对象的`text()`,`html()`方法获取节点的文本和原始HTML文本：



	<!-- HTML结构 -->
	<ul id="test-ul">
    		<li class="js">JavaScript</li>
    		<li name="book">Java &amp; JavaScript</li>
	</ul>

分别获取文本和HTML：

	$('#test-ul li[name=book']).text(); // 'Java & JavaScript'
	$('#test-ul li[name=book]').html(); // 'Java &amp; JavaScript

设置文本和html：在`text()`方法中传入参数即可。

一个jQuery对象可以包含0个或任意个DOM对象，它的方法实际上会作用在对应的每个DOM节点上。

jQuery对象的另一个好处是我们可以执行一个操作，作用在对应的一组DOM节点上。即使选择器没有返回任何DOM节点，调用jQuery对象的方法仍然不会报错

## 修改CSS
jQuery对象有“批量操作”的特点，这用于修改CSS实在是太方便了。

调用方法`css('name', 'value')`

jQuery对象的所有方法都返回一个jQuery对象（可能是新的也可能是自身），这样我们可以进行链式调用

`css()`方法其他用法：

	var div = $('#test-div');
	div.css('color'); // '#000033', 获取CSS属性
	div.css('color', '#336699'); // 设置CSS属性
	div.css('color', ''); // 清除CSS属性

为了和JavaScript保持一致，CSS属性可以用'background-color'和'backgroundColor'两种格式。

`css()`方法作用于DOM节点的`style`属性，具有最高优先级：

	var div = $('#test-div');
	div.hasClass('highlight'); // false， class是否包含highlight
	div.addClass('highlight'); // 添加highlight这个class
	div.removeClass('highlight'); // 删除highlight这个class

## 显示和隐藏DOM
隐藏DOM可以设置CSS的`display`的属性为`none`,利用`css()` 方法可以实现，但是如果要恢复原有的`display`就需要记下原来的`display`属性到底是`block`还是`inline`还是别的值。

在jQuery中可以直接使用`show()`,`hide()`:

	var a = $('a[target=_blank]');
	a.hide();
	a.show();

## 获取DOM信息

	// 浏览器可视窗口大小:
	$(window).width(); // 800
	$(window).height(); // 600

	// HTML文档大小:
	$(document).width(); // 800
	$(document).height(); // 3500

	// 某个div的大小:
	var div = $('#test-div');
	div.width(); // 600
	div.height(); // 300
	div.width(400); // 设置CSS属性 width: 400px，是否生效要看CSS是否有效
	div.height('200px'); // 设置CSS属性 height: 200px，是否生效要看CSS是否有效

`attr()`,`removeAttr()`方法操作DOM节点的属性：

	// <div id="test-div" name="Test" start="1">...</div>
	var div = $('#test-div');
	div.attr('data'); // undefined, 属性不存在
	div.attr('name'); // 'Test'
	div.attr('name', 'Hello'); // div的name属性变为'Hello'
	div.removeAttr('name'); // 删除name属性
	div.attr('name'); // undefined

`prop()`方法

	var radio = $('#test-radio');
	radio.prop('checked'); // true;

`is()`:

	radio.is(':checkoed'); // true

## 操作表单

对于表单操作，使用`val()`方法获取和设置对应的`value`属性：

统一各种输入框的取值和赋值操作。

## 修改DOM结构

### 添加DOM

- `html()`
- `append()`

.

	<div id="test-div">
    		<ul>
        		<li><span>JavaScript</span></li>
        		<li><span>Python</span></li>
        		<li><span>Swift</span></li>
   	 	</ul>
	</div>

先拿到`<ul>`节点：

	var ul = $(`#test-div>ul`);

调用`append()`传入HTML片段：

	ul.append(`<li><span>Haskell</span></li>');

`append()`方法还可以接受jQuery对象和函数对象，Dom对象

`prepend()`把内容加到DOM的最前，`after()`把内容加到当前节点的后面

### 删除节点
`remove()`

....终止学习，开始看Python
