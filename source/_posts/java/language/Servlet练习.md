---
title: primitive 主数据类型和引用
date: 2016-03-25 21:18:12
tags: java基础
category: java基础
---

1. 写MyServlet
	1. 还不行
	


## 客户端

模拟网页登入

1. 普通api
	1. get
	2. postr

2. HttpClient
	1. get
	2. post

1. 有字符集问题
2. 无字符集问题

## sqlite数据库

database:存储数据的仓库

存储内存中的数据的方式：

1. 序列化
2. 数据库：
	1. 安全
	2. 可以存储大数据
	3. 目前市面上通用的解决方案
3. xml

常用的数据库：
服务端的数据库：

1. oracle：oracle
2. ibm：db2
3. oracle：mtsql
4. microsoft：sqlserver

移动客户端的数据库：

1. 一个开源组织开发的：sqlite

sqlite定义：轻量级无服务器支持的事务性的数据库

sqlite操作的数据：数据库文件

<!--more-->

sqlite特点：

1. 不需要服务器（不需要有专门的服务器）->即是客户端也是服务器
2. 可以嵌入到应用程序中，如：firefox，android，adobe,apple mac os x,McAfee,
3. 支持标准的sql查询语言
4. 弱类型的数据库：语法不严谨


SQl：结构化查询语言，用于操作数据库的专用语言

sqlite数据库常用的指令：

1. .databases         
2. .echo ON|OFF   回显开关
3. .exit
4. .help
5. .load FILE ?ENTRY?
6. .open ?FILENAME?    打开一个数据库（没有就创建）
7. .quit
8. read FILENAME  加载执行sql脚本（sql脚本：包含了多条sql语句的文件）
9. .show 
10. .log ?FILENAME? 
11. .echema ?FILENAME表名? 查看表结构


sql数据库常用操作：

1. 创建表

		create table 表名（
			字段名1	类型2	约束信息或者默认值	，
			字段名2	类型2	约束信息或者默认值，
		。。。。。
		）；

	表：数据库中的数据存储在表中，每一条记录代表一条数据

	表名：要求见名知意，以“tb_”开头

	字段名：与java合法标识符一样

	类型：

	1. integer
	2. char：定长的字符串
	3. varchar：变长的字符串
	4. text：字符串数据量大
	5. numeric：小数

	primary key :主键 （一个表中某条数据的唯一标识）不能重复，否则报错
	
	autoincrement:自增长（从1开始往后计数）

2. 删除表

		drop table 表名;

3. 插入表记录

		inset into 表名(字段名1，字段名2，。，，字段名n)  
				values(值1，值2.。。。。值n);

4. 查看表记录

		select * from tb_name [where 语句];
		select 字段1，字段2 from tb_name [where 语句];

5. 删除记录

		delete  from tb_name;
		delete from tb_name [where 语句]；语句的类型是boolean,只将满足条件的记录删除。

6. 修改表记录

		update ta_name set 字段名1=字段值1，字段名2=字段值2.。。。[where 语句]；

7. where语句的常用语法：

		=：等于   > ; < ; <= ; >= ; 
		<>:不等于
		!=:不等于
		like:模糊匹配， + ‘%xxx’：前面任意后面固定； + ‘xxx%’：前面固定后面任意； （+ ‘%xxx%’：中间字符）不行，google
		布尔表达式 and 布尔表达式 ：并关系
		布尔表达式 or 布尔表达式 ： 或关系
		between xxx and xxx ：并
		in (常量值1，常量值2，。。)：字段是否在指定的值中存在

扩展：

crud：

1. 约束：为了防止表中出现冗余数据，通过添加限定，来对插入表中的记录进行筛选

	常用约束：

	1. 主键约束：primary key
	2. 唯一约束：unique
	3. 检查约束：chack (条件)
	4. 外键约束(foreign key)： foreign key(字段名2) references 主表 (主键)
	5. not null:

		
2. 修改表结构

		alter table tb_name add 字段名 类型;
		alter table tb_student add score numeric(4,2) default 80.23;
		
3. 打开sqlite数据库中的外键约束的设置

		pragma foreign_keys=on;

4. 使用eclipse连接sqlite数据库，可视化的客户端
	
	说明：eclipse内部有内嵌的sqlite数据库

5. 连接查询(严格匹配的记录才能显示出来)

		select name 名字,score 分数 from tb_student stu,tb_score s where stu.id=s.id 
		1. 连接查询：select name 名字,score 分数 from tb_student stu,
									join tb_score s 
									on stu.id=s.id
		2. 左连接查询：select name 名字,score 分数 from tb_student stu,
									left join tb_score s 
									on stu.id=s.id
		3. 右连接查询：select name 名字,score 分数 from tb_student stu,
									right join tb_score s 
									on stu.id=s.id

6. 常用的聚合函数
	
		count：记录条数
		max(score)：求最大值
		min():
		sum():
		avg(): 

7. 排序

		升序：asc，降序：desc

8. 分组查询语法

		select 字段列表 from tb_name group by 字段

9. 子查询语句(嵌套查询)

		select 字段列表	from (select * from tb_name);

10. 数据库结构设计：
	1. 1vn时在n方维护关系
	2. 1v1时需要实际判断