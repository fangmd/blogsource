---
title: Hibernate 使用
date: 2018-12-06 21:18:12
tags: [java]
category: java
---

介绍：Hibernate 是 dao 层框架，用于操作数据库，以面向对象的方式操作数据库。

orm 思想：对象关系映射，通过映射文件配置对象与数据库中表的关系。

# 配置文件

## 主配置文件

命名固定：`hibernate.cfg.xml`

格式:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
    <session-factory>
        <!-- ..属性.. -->
        <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>

        <!-- 注册 orm mapping -->
        <mapping resource="com/passon/hibernate/domain/Customer.hbm.xml" />
    </session-factory>
</hibernate-configuration>
```

所有配置键值对在 sdk 地址 `hibernate-release-5.3.7.Final/project/etc/hibernate.properties` 中。

### 必须配置项目

数据库配置，以 mysql 为例子：

```xml
<!-- 数据库驱动 -->
<property name="hibernate.connection.driver_class">com.mysql.jdbc.Drivr</property>
<!-- 数据库url -->
<property name="hibernate.connection.url">jdbc:mysql:///hibernate</property>
<!-- 数据库连接用户名 -->
<property name="hibernate.connection.username">root</property>
<!-- 数据库连接密码 -->
<property name="hibernate.connection.password">happy0224</property>
<!-- 数据库方言
    不同的数据库中,sql语法略有区别. 指定方言可以让hibernate框架在生成sql语句时.针对数据库的方言生成.
    sql99标准: DDL 定义语言  库表的增删改查
                DCL 控制语言  事务 权限
                DML 操纵语言  增删改查
            注意: MYSQL在选择方言时,请选择最短的方言.
    -->
<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
```

### 可选配置项目

```
<!-- 将hibernate生成的sql语句打印到控制台 -->
<property name="hibernate.show_sql">true</property>
<!-- 将hibernate生成的sql语句格式化(语法缩进) -->
<property name="hibernate.format_sql">true</property>

<!-- 
    ## auto schema export  自动导出表结构. 自动建表
    #hibernate.hbm2ddl.auto create      自动建表.每次框架运行都会创建新的表.以前表将会被覆盖,表数据会丢失.(开发环境中测试使用)
    #hibernate.hbm2ddl.auto create-drop 自动建表.每次框架运行结束都会将所有表删除.(开发环境中测试使用)
    #hibernate.hbm2ddl.auto update(推荐使用)    自动生成表.如果已经存在不会再生成.如果表有变动.自动更新表(不会删除任何数据).
    #hibernate.hbm2ddl.auto validate    校验.不自动生成表.每次启动会校验数据库中表是否正确.校验失败.
-->
<property name="hibernate.hbm2ddl.auto">update</property>
```





## ORM 元数据配置

例子: `Customer.hbm.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
   <!-- package属性:填写一个包名.在元素内部凡是需要书写完整类名的属性,可以直接写简答类名了. -->
<hibernate-mapping package="com.passon.hibernate.domain" >
    <!-- 
        class元素: 配置实体与表的对应关系的
            name: 完整类名
            table:数据库表名
     -->
    <class name="Customer" table="cst_customer" >
        <!-- id元素:配置主键映射的属性
                name: 填写主键对应属性名
                column(可选): 填写表中的主键列名.默认值:列名会默认使用属性名
                type(可选):填写列(属性)的类型.hibernate会自动检测实体的属性类型.
                        每个类型有三种填法: java类型|hibernate类型|数据库类型
                not-null(可选):配置该属性(列)是否不能为空. 默认值:false
                length(可选):配置数据库中列的长度. 默认值:使用数据库类型的最大长度
         -->
        <id name="cust_id"  >
            <!-- generator:主键生成策略 -->
            <generator class="native"></generator>
        </id>
        <!-- property元素:除id之外的普通属性映射
                type(可选):填写列(属性)的类型.hibernate会自动检测实体的属性类型.
                        每个类型有三种填法: java类型|hibernate类型|数据库类型
                not-null(可选):配置该属性(列)是否不能为空. 默认值:false
                length(可选):配置数据库中列的长度. 默认值:使用数据库类型的最大长度
         -->
        <property name="cust_name" column="cust_name" not-null="true" length="32">
            <!--  <column name="cust_name" sql-type="varchar" ></column> -->
        </property>
        <property name="cust_source" column="cust_source" ></property>
    </class>
</hibernate-mapping>
```

# API

简单的保存数据例子：

```java
        Configuration conf = new Configuration().configure();

        SessionFactory sessionFactory = conf.buildSessionFactory();

        Session session = sessionFactory.openSession();

        Transaction tx = session.beginTransaction();
        //----------------------------------------------
        Customer c = new Customer();
        c.setCust_name("google公司");

        session.save(c);//执行保存

        //----------------------------------------------
        tx.commit();
        session.close();
        sessionFactory.close();
```

## 相关类

配置加载类，用于加载主配置 和 orm 元数据加载。

```java
// 创建对象
Configuration conf = new Configuration();
// 读取 配置文件
conf.configure();

// 可选：读取指定 orm 元数据(扩展), 如果主配置中已经引入映射配置，不需要手动加载
//configure.addResource(resourceName);
//configure.addClass(persistentClass);

// 工厂对象，用于创建 Session
// 1. SessionFactory 负责保存和使用所有配置信息，对象创建消耗内存大
// 2. SessionFactory 线程安全
// 在使用的时候，尽量保证工程只使用一个 SessionFactory 对象
SessionFactory sessionFactory = conf.buildSessionFactory();

// 获取 Session
// Session 对象：表示 Hibernate 框架 和 数据库之间的连接回话
// Session 是操作数据库的核心对象
Session session = sessionFactory.openSession();
// 获取一个和该线程绑定的 Session 对象
//Session currentSession = sessionFactory.getCurrentSession();

// 开启事务
Transaction tx = session.beginTransaction();
```

操作数据库：

```
Customer c = session.get(Customer.class, 1);
//session.save(c);
//session.update(c);
session.delete(c);
```

释放资源:

```java
tx.commit();
//tx.rollback();
session.close();
sessionFactory.close();
```

# HibernateUtils

```java
package com.passon.hibernate.utils;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class HibernateUtils {

    private static Configuration conf = new Configuration().configure();
    private static SessionFactory sessionFactory = conf.buildSessionFactory();

    public static Session getSession() {
        return sessionFactory.openSession();
    }

    public static Session getCurrentSession() {
        return sessionFactory.getCurrentSession();
    }


    public static void close(Session session) {
        session.close();
    }
}
```

# Hibernate 中实体规则

## 实体类创建需要注意

1. 实体必须提供无参构造函数

Hibernate 查询的时候通过反射创建对象，需要无参构造函数。

2. 成员变量私有，提供 `getter/setter`
3. 持久化类中的属性，尽量使用包装类型(Integer, Long, Double, Float)
4. 持久化需要提供 oid,与数据库中的主键列对应
5. 不要使用 final 修饰 class

Hibernate 使用 cglib 代理生成对象，代理对象是继承被代理对象的。

## 主键类型

1. 自然主键

表中业务列中，有一个列可以作为主键使用。（比如：身份证号）

2. 代理主键(常见主键类型)

创建一个没有业务意义的列作为主键，比如：userId, ...

## 主键生成策略

每条记录录入时，主键生成规则

在 orm 元数据配置中设置：

```
<!-- 主键自增，由数据库自己维护主键，录入时不需要指定主键 -->
<generator class="identity"></generator>

<!-- （效率低，有并发问题）主键自增，由hibernate来维护，每次录入的时候会先查询一次获取表中最大值，录入时不需要指定主键 (开发时不使用)-->
<generator class="increment"></generator>

sequence: Oracle中的主键生成策略。

hilo: 高低位算法，主键自增，由hibernate来维护(开发时不使用)

native: hilo+sequence+identity, 自动三选一

uuid：随机字符

assigned: 自然主键生成策略，用户自己录入
```

# Hibernate 中的对象状态

1. 瞬时状态

没有ID，没有与 session 关联

`session.save` 函数将 瞬时状态->持久化状态

2. 持久化状态

有id，与 session 有关联

特点：这个状态下，对象的任何变化都会自动同步到数据库中。

`session.get` 函数获取持久化状态对象。
`session.close` 后会把 持久化状态->游离状态
`session.delete`: 持久化状态->瞬时状态
`session.saveOrUpdate`: 游离状态, 瞬时状态->持久化状态

3. 游离|托管状态

有id，没有与 session 关联

可以使用 `session.update` 函数，游离状态->持久化状态。

# Hibernate 一级缓存&快照

目的：提高效率

```
Customer c = session.get(Customer.class, 1);
Customer c2 = session.get(Customer.class, 1);

// c == c2 : true
```

缓存具有快照功能。修改后的对象和快照对比，判断是否需要进行 sql update 语句。
减少不必要的sql语句

# Hibernate 事务

事务特性：原子性，一致性，隔离性，持久性

事务并发问题：

1. 脏读：读到某个人正在操作但是还没有提交的数据，
2. 不可重复读：两次重复读取，数据不一致
3. 幻读：删除表后，发现有人往表里插入了一条数据

事务隔离级别：

读未提交：123
读已提交：23
可重复读：3 (mysql 默认)
串行花：没有问题

在 Hibernate 中执行隔离基本，在基础配置文件中：

```
#hibernate.connection.isolation 1,2,4,8 ; 0001 0010 0100 1000
```

如何管理事务：需要保证 dao 层和 service 层的 session 对象是同一个。

```
session.getCurrentSession() 保证是当前线程的 session.
```

在配置中指定 session 与当前线程绑定:

```xml
<property name="hibernate.current_session_context_class">thread</property>
```

>通过 getCurrentSession 获取的 session 开启事务后，提交事务会自动关闭 session, 不需要手动关闭 session

# Hibernate 批量查询

## HQL

>Hibernate 自创

```java
String hql = "from com.passon.hibernate.domain.Customer"
// String hql = "from Customer where cust_id=1"
Query query = session.cureateQuery(hql);
List<Customer> list = query.list();
Customer c = (Customer)query.uniqueResult();

// String hql = "from Customer where cust_id=?"
// Query query = session.cureateQuery(hql);
// query.setParameter(0, 1l); // 设置参数, 参数一表示 ? 位置
// query.setParameter("cust_id2", 1l); // 设置参数， 对应hql, from Customer where cust_id=:cust_id2
// query.setLong(0, 1l);
```

分页

```java
query.setFirstResult(); // 从第几条数据开始
query.setMaxResults(); // 数据数量
```

## Criteria
>Hibernate 自创, 无语句查询

```java
Criteria criteria = session.createCriteria(Customer.class); // 查询所有
List<Customer> list = criteria.list();
//Customer c = criteria.uniqueResult();

//条件查询
criteria.add(Restrictions.....)

//分页
criteria.setFirstResult(.);
criteria.setMaxResults(.)
```

## sql

```java
String sql = "select * from cst_customer";
SQLQuery query = session.createSQLQuery(sql);
//List<Object[]> list = query.list();
//query.uniqueResult();


//指定结果集封装对象
query.addEntity(Customer.class);
List<Customer> list = query.list();

//条件查询 sql = "select * from cst_customer where cust_id=?";
query.setParameter(0, 1l);
Customer c = (Customer)query.uniqueResult();
```

# 多表关系

## 一对多

```java
class Customer{
    private String cid;
    private String name;
    private Set<LinkMan> linkMens = new HashSet<LinkMan>();
    //getter/setter
}

class LinkMan{
    private String lid;
    private String name;
    private Customer customer;
    //getter/setter
}
```

Customer 特殊配置：

```xml
<!-- 1对多 -->
<set name="linkMens">
    <key column="lid"></key>
    <one-to-many class="LinkMan"/>
</set>
```

LinkMan 特殊配置：

```xml
<!-- 多对1 -->
<many-to-one name="customer" column="lid" class="Customer"></many-to-one>
```

### 操作

```java
Customer c = new Customer()
c.setName("fang");

LinkMan lm = new LinkMan();
lm.setName("fang-c01");
LinkMan lm2 = new LinkMan();
lm.setName("fang-c02");

//设置关系
c.getLinkMans().add(lm);
c.getLinkMans().add(lm2);
lm.setCustomer(c);
lm2.setCustomer(c);

//保存
session.save(c);
session.save(lm);
session.save(lm2);
```

简化保存：自动保存关联对象。

设置 级联操作

```xml
<!-- 
    级联操作 cascade:
        save-update
        delete
        all
 -->
<set name="linkMens" cascade="save-update">
    <key column="lid"></key>
    <one-to-many class="LinkMan"/>
</set>

//保存的时候
session.save(c);//即可
```

inverse 优化 sql: 提升性能，优化关系维护

```xml
<!-- true: 将关系维护交给 LinkMan ; false(默认值)-->
<set name="linkMens" inverse="true">
    <key column="lid"></key>
    <one-to-many class="LinkMan"/>
</set>


//同时可以省略, 下面两行代码
//c.getLinkMans().add(lm);
//c.getLinkMans().add(lm2);
```



## 多对多




