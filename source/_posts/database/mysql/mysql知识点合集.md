---
title: MySQL记录
date: 2019-09-22 13:18:12
tags: [MySQl]
category: Database

---


# 索引

1. 对数据建立目录字典，达到快速查找数据的作用。
2. 运用在表中某些字段上，但是存储时，独立于表之外
3. 拿空间换时间

索引特点:

1. 用户不需要在 sql 中指定使用哪个索引
2. 定义了 primary key 和 unique 约束时会自动在对应列上创建索引
3. 用户可以按照需要对指定列创建索引

**什么时候要索引:**

1. 表经常进行 查询/select 操作
2. 表很大，记录内容分布范围广
3. 列名经常出现在 where 子句或连接条件中

**什么时候不要索引:**

1. 表经常进行 增/删/改 insert/update/delete 操作
2. 表很小
3. 列名不常作为连接条件或出现在where子句中

**优缺点**

1. 加快查询速度，降低插入 删除 修改速度(因为增删改同时要维护索引所以慢)
2. 索引占用物理和数据空间

**索引分类:**

1. 唯一索引
2. 主键索引
3. 聚集索引
4. 非聚集索引


# 事务隔离级别

事务4基本要素: ACID, 原子性 Atomicity, 一致性 Consistency, 隔离性 Isolation, 持久性 Durability


1. Serializable 可串行【可避免脏读，不可重复读，虚读】
2. Repeatable read 可重复读【可避免脏读，不可重复读】
3. Read committed 读已提交 【可避免脏读】
4. Read uncommitted 读未提交【级别最低，什么都避免不了】

```
TRANSACTION_READ_UNCOMMITTED
TRANSACTION_READ_COMMITTED
TRANSACTION_REPEATABLE_READ
TRANSACTION_SERIALIZABLE
```

1. 赃读: 一个事务读取到另一个事务未提交的数据

例子：A给B转账，A 还没有提交事务，B 已经能读取到金额增加的数据了。但是实际上不能保证 A 转账成功。

2. 不可重复读: 一个事务读取到另外一个事务已经提交的数据，也就是说一个事务可以看到其他事务所做的修改

例子：A 事务开始和结束是都读取了一个数据，但是 B 事务**修改**了数据并提交了，导致 A 事务两次读取的数据不一致

3. 虚读/幻读: 是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。

例子：A 事务开始和结束是都读取了一个数据，但是 B 事务**插入**了数据并提交了，导致 A 事务两次读取的数据不一致

> 脏读是不可容忍的，不可重复读和虚读在一定的情况下是可以的【做统计的肯定就不行】。

# 数据库乐观锁和悲观锁






# question

## Access denied for user 'root'@'localhost' (using password: YES)

```
> mysql -u root -p

Access denied for user 'root'@'localhost' (using password: YES)

mysql 密码错误
```

参考:

1. [https://juejin.im/post/5a9ca0d6518825555c1d1acd#heading-38](https://juejin.im/post/5a9ca0d6518825555c1d1acd#heading-38)