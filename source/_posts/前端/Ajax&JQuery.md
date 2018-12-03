---
title: AJax & JQuery
date: 2018-11-21 13:18:12
tags: [js]
category: 前端

---

# Ajax

Ajax: 异步的 JavaScript 和 XML

js 请求网络

内部使用 XmlHttpReuqest 发起请求。

## 数据请求

创建请求对象
```js
        function ajaxFunction() {
            var xmlhttp;
            if (window.XMLHttpRequest) {// code for IE7+, Firefox, Chrome, Opera, Safari
                xmlhttp = new XMLHttpRequest();
            }
            else {// code for IE6, IE5
                xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
            }
            return xmlhttp;
        }
```

get

```js
        function getData() {
            //1. 创建请求对象
            var xmlHttp = ajaxFunction();

            //2. 设置请求，发起请求
            xmlHttp.open("GET", "simpleGet?name=周杰伦", true);

            //3. 获取响应
            xmlHttp.onreadystatechange = function () {
                if (xmlHttp.readyState == 4 && xmlHttp.status == 200) {
                    alert(xmlHttp.responseText);
                }
            };

            // 4. 发起请求
            xmlHttp.send();
        }
```

post

```js
        function postData() {
            //1. 创建请求对象
            var xmlHttp = ajaxFunction();

            //2. 设置请求，发起请求
            xmlHttp.open("POST", "simpleGet", true);
            xmlHttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");

            //3. 获取响应
            xmlHttp.onreadystatechange = function () {
                if (xmlHttp.readyState == 4 && xmlHttp.status == 200) {
                    alert(xmlHttp.responseText);
                }
            };

            // 4. 发起请求
            xmlHttp.send("name=1111&age=12");
        }
```

# JQuery

JavaScript 代码框架

作用：简化代码，提高效率。

核心：write less do more.

设置值：

```js
//找到 id=text01 的元素，设置 value 属性值
$("#text01").val(responseText);

$("#text01").text(responseText);
$("#text01").html(responseText); // 内容可以为 html

$("#text01").append(responseText); //添加值
```

入口函数：

```js
$(function(){

})
// or
$(document).ready(function(){

})
```


xml 格式数据操作：

```js
/**
<list>
  <city >
    <id>1</di>
    <pid>1</pdi>
    <cname>深圳</cname>
  </city>
  <city>
    <id>1</di>
    <pid>2</pdi>
    <cname>东莞</cname>
  </city>
</list>
*/
// 从 data 找出所有的 City 并 遍历 city
$(data).find("city").each(function(){
  var id = $(this).children("id").text();
  var cName = $(this).children("cname").text();
})
```

json 格式数据操作：

```js
/**
[
  {
    "id": 1,
    "pid": 1,
    "cname": "深圳"
  },
  {
    "id": 1,
    "pid": 2,
    "cname": "东莞"
  }
]
*/
$(data).each(function(index, c){
  alert(c.cname);
})
```

## ajax + jquery

load:

```js
$("#text01").load("simpleGet?name=周杰伦", function (responseText, statusText) {
  alert(responseText + statusText);
});
```

get:

```js
$.get("simpleGet?name=周杰伦", function(data, status){
  alert(data+status);
});
```

post:

```js
$.post("simpleGet", {name: "周杰伦"}, function(data, status){

})
```















