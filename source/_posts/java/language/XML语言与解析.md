---
title: XML语言
date: 2016-03-25 21:18:12
tags: java基础
category: java基础

---

## XML语言
>扩展标记语言
### XML语言结构
1. 声明部分
2. 元素部分（内容部分，节点）

### 语言特点
1. 大小写敏感
2. 标签可以任意扩展（html不能扩展）
3. 标签之间可以嵌套，切记交叉嵌套
4. 只有一个跟节点

### XML文件的作用
1. 配置信息（web.xml：web工程的入口）
2. 存储数据

<!--more-->

xml:外号“数据存储之王”

## 文档声名

```
<?xml version="1.0" encode="utf-8" >
```

## 特殊字符

1. 使用转义字符
2. 使用 `CDATA` 区域


## DTD 约束

Document Type Definition

规定 xml 文档中，元素名称， 子元素名称，子元素顺序，子元素属性,

```
<?xml version="1.0" encode="utf-8" >
<!DOCTYPE web-app[
		//...约束内容
]>

<web-app>
</web-app>
```

## scheme 约束
是 DTD 替代者

Schema 文档格式：xsd.

支持名称空间

## 解析

### xml解析技术
1. dom：基于类，将xml文件装载到内存进行分析，需要把所有xml都解析到内存中-->对内存容量要求高
2. dom4j：基于接口，（xml文件分批装载）便于扩展，解析xml文件速度快-->适合java ee 开发
3. sax：基于事件通知（解析每一个节点时，都会触发一个动作，通知一个方法区进行具体的解析操作）消耗内存小-->适合移动客户端（安卓）
4. pull:基于事件常量（解析到每个节点时，会触发一个事件常量，满足某个事件常量执行具体的解析操作）-->适合移动客户端（google推荐的解析方式）
## SAX解析

SAX解析技术：用于解析xml文件读取其中的数据进行分析。

### dom4j

hibernate 底层使用的解析方式

DOM 解析：

```
Document
Element
Attribute
Test
```




### SAX解析xml步骤
1. 获得解析器工厂（工厂设计模式）
2. 获得解析器对象
3. 解析指定的xml文件
4. 分析结果
5. 资源释放

### SAX涉及的API
1. SAXParserFactory ：解析器工厂抽象类
	1. static newInstance():构建解析器工厂对象
	2. newSAXParse():获得解析器对象

2. SAXParser：解析器抽象类
	1. parse(File,DefaultHandler):解析指定xml文件

3. DefaultHandler:解析操作具体实施类(普通类，方法的方法体是空)
	1. characters(char[] , int,int):读取标签体内容时触发该方法的执行
	2. endDocument():xml文档解析结束触发执行
	3. endElement(String,String,String):xml中每一个标签结束触发执行
	4. startDocument():
	5. startElement(String,String,String,Attributes):xml每个标签开始会触发

4. Attributes:对xml节点中属性的抽取和封装
	1. getLength():返回属性的个数
	2. getQName(int):根据参数指定的索引值获得属性的名字
	3. getValue(int):根据参数指定的索引值获得属性的值

## pull解析

### pull涉及的api
1. XMLPullParserFactory:解析器工厂
	1. static newInstance():
	2. newPullParser()
2. XMLPullParser：解析器抽象类
	1. 事件常量：END_DOCUMENT:int 文档解析结束
	2. START_DOCUMENT:
	3. START_TAG:标签解析开始
	4. END_TAG
	5. TEXT:标签体解析
	6. 方法：getAttributeCount():获取标签中的属性
	7. getAttributeName(int):获取索引值的属性名
	8. getAttributeValue(int):获取索引值的值
	9. getEventType():获取事件常量值
	10. getName():获取节点名
	11. getText():获取标签体（内容）
	12. next():下一个标签
	13. nextTag()；获取下一个标签名
	14. nextText():下一个标签体
	15. setInput(...):设置要解析的xml文件
