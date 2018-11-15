---
title: sql 学习记录
date: 2018-10-24 13:18:12
tags: [sql]
category: Database

---

sql: Structure Query Language 结构化查询语言

ddl: 数据定义语言，create drop alter

dml：数据操纵语言， insert update delete

dcl: 数据控制语言, 定义访问权限，安全设置，取消访问权限，grant

DQL: 数据查询语言，select from whete


# 数据库 CRUD

```
# 查看 mysql 中所有数据库
show databases;

information_schema:
mysql:
perfomance_schema:

# 创建数据库 数据库名称，字符集，校对规则
create database db_example;
create database db_example character set utf8;
create database db_example character set utf8 collate utf8_bin;

# 切换当前数据库
use db_example;

# 查看当前正在使用的数据库
select database();

# 查看数据库创建的语句
show create database db_example;

# 修改数据库
alter database 数据库的名字 character set 字符集;
```

删除数据库

```
drop database 数据库名字;
```

# 表的 CRUD 操作

## 创建表

```
create table 表名(
    列名 列类型(长度) 约束,
    列名2 列类型(长度) 约束,
);

列类型：int char(固定长度) varchar(可变长度) double float boolean date(YYYY-MM-DD) time(hh:mm:ss) datetime(YYYY-MM-DD hh:mm:ss)默认是null timestamp(YYYY-MM-DD hh:mm:ss)默认使用当前时间 text(存放文本) blob(存放二进制)
长度：mysql高版本中，代表字符个数，和是否是汉子无关

列的约束:
    主键约束: primary key
    唯一约束: unique
    非空约束: not null
```

## 查看表

```
show tables;

查看表定义：查看表创建时使用的 sql 语句
show create table 表名称;

查看表结构：
desc 表名;
```

## 修改表：

```
添加列：add
alter table 表名 add 列名 列类型 列约束;

修改列：modify
alter table 表名 modify 列名 列类型 列约束;
alter table student modify sex varchar(2);

修改列名：change
alter table 表名称 change 旧列名 新列名 列类型(列长度) 列约束;

删除列：drop
alter table 表名 drop 列名;

修改表名称：to
remane table 表名 to 表名;

修改表字符集：
alter table 表名 character set 字符集;
```

## 删除表：

```
drop table 表名;
```

# 表中记录的 CRUD

## 插入数据

```
# 插入全数据
insert into 表名(列名1， 列名2, 列名3) values(值1, 值2, 值3);
insert into 表名 values(值1, 值2, 值3);

# 批量插入
insert into student values(值1, 值2, 值3),(值1, 值2, 值3),(值1, 值2, 值3);
```

批量插入的效率比单条插入高，需要处理的语句少。
批量插入数据事务操作。

## 删除记录

```
delete from 表名 [where 条件];

没有指定条件就会删除全部数据。

delete: DML 一条一条删除表中数据
truncate: DDL 先删除表，再重建表 
哪个效率高？：数据量少的时候 delete 高效，数据量多的时候 truncate 高效。

删除表中所有数据：
delete from table_name;
truncate table table_name
```

## 更新表记录

```
update 表名 set 列名=列值, 列名2=列值2 [where 条件];

参数如果是字符串，日期 需要加单眼号。
```

## 查询记录

```
select [distinct] [*] [列名，列名2] from 表名 [where 条件] [group by having] [order by];

distinct: 去除重复的数据

查询所有数据:
select * from 表名;

查询所有商品，显示指定列：
select [列名, 列名] from 表名；

别名查询： as 可以省略
    表别名
    select p.pname, p.price from product as p;
    列别名
    select pname as 商品名称, price as 商品价格  from product;

去重复：
select distinct price from product;

select 运算查询：(在查询结果上做了运算):
select *, price*1.5 as 加价格 from product;
select pname, price, price*1.5 as 加价格 from product;

条件查询 where: 指定条件，确定要操作的记录
select * from product where price > 60;

    where 后的条件写法
        -- 关系运算 > >= < <= = != <> (不等于)
        -- 逻辑运算 and or not
        -- 模糊查询 like, _代表一个字符，% 代表多个字符
            select * from product where pname like '_熊%';
        -- 在某个范围值内 in
            select * from product where cno in (1,4,5);
        -- 排序查询 order by 关键字 asc: ascend 升序(默认), desc descend 降序
            select * from product order by price;
            select * from product order by price desc;
            select * from product where pname like '%小%' order by price desc;

聚合函数
    sum()
        select sum(price) from product;
    avg(),
        select avg(price) from product;
    count(),
        select count(price) from product;
    max(),
        select max(price) from product;
    min(),
        select min(price) from product;

    where 后面不能使用聚合函数！
        (错误)select * from product where price > avg(price); 
        (正确)select * from product where price > (select avg(price) from product);

分组：group by

    根据字段分组, 分组后统计个数：
        select cno, count(*) from product group by cno;
    根据字段分组,
        select cno, avg(price) 
        from product group by cno
        having avg(price) > 60;
    having 关键字，可以接聚合函数，出现在分组之后，
    where 关键字，不能接聚合函数，出现在分组之前

编写顺序：

    select .. from .. where .. group by .. having .. order by

执行顺序
    
    from .. where .. group by .. having .. select .. order by

```

# 多表查询

## 分析

建立数据库原则：

1. 一个项目一个数据库

多表之间的关系如何维护：

1. 外键约束
    alter table product add foreign key(cno) references category(cid);
2. 多表之间的建表原则
    一对多：多的一边加外键
    多对多：通过中间表维护关系，中间表至少有两个外键分别指向两个表。
    一对一：一张表维护；两张表使用同一个 id 作为主键；使用外键
            拆表操作：将常用信息和不常用信息拆开，减少臃肿

商城例子：

```
create table user(
    uid int primary key auto_increment,
    username varchar(31),
    password varchar(31),
    phone varchar(11)
);

insert into user values(null, '章三', '111111', '15669262390');

订单表：
create table orders (
    oid int primary key auto_increment,
    sum int not null,
    otime timestamp,
    address varchar(100),
    uno int,
    foreign key(uno) references user(uid)
);

insert into orders values(null, 200, null, '北京海淀', 1);
insert into orders values(null, 234, null, '北京朝阳', 1);

商品分类表：
create table category(
    cid int primary key auto_increment,
    cname varchar(15),
    cdesc varchar(100)
);

insert into category values(null, '手机数码', '电子产品');
insert into category values(null, '鞋靴箱包', '江南皮革厂');
insert into category values(null, '香烟水酒', '茅台，二锅头');
insert into category values(null, '酸奶饼干', '哇哈哈，蒙牛伊利');
insert into category values(null, '馋嘴零食', '瓜子花生，八宝粥');

商品表:
create table product(
    pid int primary key auto_increment,
    pname varchar(10),
    price double,
    cno int,
    foreign key(cno) references category(cid)
);

insert into product values(null, '小米mix2', 998, 1);
insert into product values(null, '锤子', 2998, 1);
insert into product values(null, 'adidas', 299, 2);
insert into product values(null, '老村长', 89, 3);
insert into product values(null, '劲酒', 35, 3);
insert into product values(null, '小熊饼干', 1, 4);
insert into product values(null, '卫龙辣条', 1, 5);
insert into product values(null, '旺旺血饼', 1, 5);

订单项目：中间表(订单-商品)
create table orderitem(
    ono int,
    pno int,
    foreign key(ono) references orders(oid),
    foreign key(pno) references product(pid),
    ocount int,
    subsum double
);

insert into orderitem value(1, 7, 100, 100);
insert into orderitem value(1, 8, 101, 101);

insert into orderitem value(2, 5, 1, 35);
insert into orderitem value(2, 1, 3, 998);
```

## 多表查询

### 交叉连接查询，笛卡尔积：

```
select * from product;
select * from category;

(无意义)select * from product, category;
(过滤有意义的数据) select * from product as p, category as c where p.cno=c.cid;
(过滤有意义的数据) select * from product p, category c where p.cno=c.cid;
```

内连接查询：

```
-- 隐式内连接
select * from product p, category c where p.cno=c.cid;

--显示内连接
select * from product p INNER JOIN category c ON p.cno=c.cid;

区别：
隐式连接：在查询的结果上做条件过滤
显示连接：带着条件去查询结果，效率高

左外连接：保证左表全，右边没有对应的数据使用 NULL 填充
SELECT * FROM product p LEFT OUTER JOIN category c ON p.cno=c.cid;
右外连接：保证右表全
SELECT * FROM product p RIGHT OUTER JOIN category c ON p.cno=c.cid;

INSERT INTO product values(null, 'NIKE', 800, NULL);
```

内连接:交集；
左外连接：左边表全部；
右外连接：右边表全部；

### 分页查询

```
limit 索引 个数

select * from product limit 0, 2;
select * from product limit 1, 2;

startIndex = (index-1)*2
```

### 子查询

```
查询分类为手机数码的所有商品：
select cid from category c where cname='手机数码';
select * from product p where p.cid=1;

查询商品名称和商品分类的信息:
左连接：
select p.pname,c.cname from product p LEFT OUTER JOIN category c on p.cno=cid;
子查询
select p.pname, (select cname from category c where p.cno=c.cid) 商品名称 FROM product p;
```








