---
title: json
date: 2016-03-25 21:18:12
tags: json
category: java基础
---

## json
定义：JavaScript Object Notation:轻量级的数据交互格式。数据在服务器和客户端之间进行交互。

### json数据格式的种类
1. 数组格式：['北京','上海','天津',xxx] [元素1，元素2，。。。]
2. 对象格式：{id:1,name:'张三',age:17,address:'北京'}；{键1：值1，键2：值2，。。。。}

### json与xml比较
1. json数据量更小，节约流量
2. json数据可读性差
3. json读取速度块
4. 
<!--more-->

### 解析json数据
#### json数据解析技术
1. json，google
2. Gson，google升级版
3. fast-json，阿里巴巴

使用几率一样，都要掌握

三种解析技术都不是jdk自带的，都是第三方jar包，使用时需要手动导入

#### json数据解析技术--json
涉及到的API：

1. JSONArray:对json数组格式的数据的抽取和封装
	1. JSONArray(String):将数组格式的数据封装成JSONArray的对象

	解析方法：
	2. length():获得json数组格式用的元素的个数
	3. getString(int ):更具参数指定的索引值获取json数组格式数据中的元素
	4. getJSONArray(int ):将对于索引值位置的元素(数组格式)取出，并封装到JSONArray中
	5. getJSONObject(int):将对于索引值位置的元素(对象格式)取出，并封装成JSONObject中

	存入方法：将内存中对象的属性值，存入到JSONArray对象中
	6. put(Stirng):在指定类型的数据存入到JSONArray对象中
2. JSONObject：
	1. JSONObject(String):将对象格式的json数据封装成JSONObject对象

	解析方法：
	1. getString(String):根据参数指定的key获取对应的值
	
	存入方法：
	1. put()

#### json数据解析技术--Gson：底层使用反射机制
注意：json数据中的key必须和类中的属性名一致

涉及的API

1. Gson:封装了json格式数据解析的一些方法
	1. new Gson()
	2. fromJson(JsonElement,)
	3. fromJson(String,Class<T>):将参数一指定的json格式的数据，直接封装成参数二的对象
	4. toJson(Object):将内存中的对象变成json数据


#### json数据解析技术--fast-json
涉及的API：

1. JSON：工具类，其中有很多工具方法，用于解析json格式的数据，或者将对象封装成json格式的数据发往服务器
	1. parseArray(String):将json数据解析成JSONArray对象
	2. parseObject(String,Class<T>):将json数据解析成T对象