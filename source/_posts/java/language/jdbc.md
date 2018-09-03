---
title: jdbc
date: 2016-03-25 21:18:12
tags: java基础
category: java基础
---

## db补充
1. 视图
2. 函数
3. 存储过程
4. 触发器

## jdbc
>java database conectivity java数据库连接：使用java语言操作数据库

jdbcAPI由sun公司开发，定义了一套接口，工具类。

数据库厂商生产的db和java语言是何种关系？

驱动包：就是不同数据库厂商对sun公司定义的一套jdbc接口实现的jar包。目的是让java语言可以方便的操作数据库

<!--more-->

jdbc涉及到的接口：

1. DriverManager(工具类):
	1. static Connection getConnection(String url):根据指定的url获取连接实例

2. Connection（接口）：与特点的数据库的连接（回话）
	1. 方法：Statement createStatement（）：将封装sql语句

3. Statement（接口）：用于执行静态SQL语句并返回它所生成的结果的对象
	1. 方法：ResultSet executeQuery(String sql)：将参数指定的sql语句传到数据库中执行返回一个结果集（查询）
	2. int executeUpdate(String sql):反馈影响的条数（增，删，改）

4. ResultSet（接口）:表示数据库结果集的数据表，通常通过执行查询数据库的语言生成。
	1. 方法：boolean next():判断表中是否有记录存在，并移动光标到下一条记录
	2. int getInt(String columnLabel):根据字段名获取对应的字段值

需求：使用jdbc操作sqlite数据库，演示crud操作。
	
1. 向表tb_studentinfo中插入一条新的记录
	1. 加载驱动  -->将接口的实现类加载到jvm中
	2. 获得连接实例：Connection
	3. 通过连接的实例创建Statement对象
	4. 执行sql
	5. 处理结果