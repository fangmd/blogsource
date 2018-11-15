---
title: JDBC
date: 2018-10-25 13:18:12
tags: [JDBC]
category: Java

---

JDBC: java database connectivity 数据库连接，用于执行 sql 语句的 java api。可以为多种关系数据库提供统一的访问。

JDBC 需要连接驱动，驱动是两个设备之间通讯的桥梁。驱动需要满足一定的格式，格式由设备提供商规定。

# 原理

>JDBC 一套由 Sun 公司定义的接口，其实现类由数据库厂商编写。

# 开发步骤

使用的 jar 包：

```
mysql-connnector-java-5.1.47-bin.jar
```

1. 注册驱动
2. 获取链接
3. 获取语句执行平台
4. 执行 sql 语句
5. 处理结果
6. 释放资源

>异常处理：Establishing SSL connection without server's identity verification is not recommended. 
>解决方案：Put the useSSL=false at the end of the name database:

```
    public static void main(String[] args) throws ClassNotFoundException, SQLException {

//        1。 注册驱动，Driver 中源码可知已经注册了
//        DriverManager.registerDriver(new Driver());
        // 使用反射方式注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        // 2. 获取数据库连接
        // url:  数据库地址 jdbc:mysql://ip:port//数据库名字
        String url = "jdbc:mysql://localhost:3306/jdbc_ex?useSSL=false";
        String userName = "root";
        String pwd = "happy0224";
        Connection connection = DriverManager.getConnection(url, userName, pwd);
        System.out.println(connection);
        // 3。 获取语句执行平台
        Statement statement = connection.createStatement();

        // 4. 执行 sql
        // statement.executeUpdate() 执行 insert delete update
        // 返回 int 表示影响的记录数
        int row = statement.executeUpdate("INSERT INTO sort values(null,'汽车用品2', 5000, 'haha' )");
        System.out.println(row);

        // 6. 释放资源
        statement.close();
        connection.close();

    }
```

结果处理：查询

```
        ResultSet resultSet = statement.executeQuery("select * from sort");
        while (resultSet.next()) {
            // 获取每列数据 getXXX 参数建议使用 列名String
//            resultSet.getInt("sid")
            System.out.println(resultSet.getInt("sid"));
        }

```


SQL 注入攻击：

```
select * from users where username='a' and pwd='2';


“select * from users where username='+ userName +' and pwd=" + pwd + ";”

userName = 1;
pwd = 1' or 1=1
```

防止注入攻击：

```
// 使用子接口 PreparedStatement (SQl 预编译存储，多次高效执行SQL)
String sql = "select * from users where username=? and password=?";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
//  参数替换占位符
preparedStatement.setString(1, "username");
preparedStatement.setString(2, "pwd");
//
```

更新：

```
String sql = "update sort set sname=?, sprice? where sid=?";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
preparedStatement.setString(1, "asdas");
int row = preparedStatement.executeUpdate();
```

查询：

```
String sql = "select * from sort where sid=?";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
preparedStatement.setString(1, "");
ResultSet resultSet = preparedStatement.executeQuery();
```

# JDBC 工具类

使用的 jar 包：

```
commons-dbutils-1.7.jar
```


```
package jdbc;

import java.sql.*;

public class JDBCUtils {

    private JDBCUtils() {
    }

    private static Connection connection;

    static {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            String url = "jdbc:mysql://localhost:3306/jdbc_ex?useSSL=false";
            String userName = "root";
            String pwd = "123";
            connection = DriverManager.getConnection(url, userName, pwd);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException("database connect fail");
        }
    }

    public static Connection getConnection() {
        return connection;
    }

    public static void close(Connection con, Statement statement, ResultSet resultSet) {
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (connection != null) {
            try {
                con.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}
```

# properties 配置文件

src 目录下：

`config.properties`

```
driverClass=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbc_ex?useSSL=false
```

# DBUtils

[https://commons.apache.org/proper/commons-dbutils/](https://commons.apache.org/proper/commons-dbutils/)

```
QueryRunner: Executes SQL queries with pluggable strategies for handling ResultSets. This class is thread safe.
ResultSetHandler: Interface
DbUtils: A collection of JDBC helper methods. This class is thread safe.
```

增删改：

```
    public static void delete() throws SQLException {
        QueryRunner queryRunner = new QueryRunner();
        String sql = "delete from sort where sid=?";
        int update = queryRunner.update(connection, sql, 9);
        System.out.println("delete row:" + update);
    }

    public static void update() throws SQLException {
        QueryRunner queryRunner = new QueryRunner();
        String sql = "update sort set sname=?, sprice=?, sdesc=? where sid=9";
        Object[] params = new Object[]{"null", 0, "null--"};
        int update = queryRunner.update(connection, sql, params);
        System.out.println("update row:" + update);
    }

    public static void insert() throws SQLException {
        QueryRunner qR = new QueryRunner();
        String sql = "insert into sort values(?, ?, ?, ?)";
        Object[] params = new Object[]{9, "体育用品", 299, "购买"};
        int update = qR.update(connection, sql, params);
        System.out.println("insert row:" + update);
    }
```

查询：

```
ArrayHandler: 只得到 sql 结果的第一条记录，返回值：Object[]
ArrayListHandler: 得到 sql 结果的全部数据， 返回值：List<Object[]>
BeanHandler: Convert the first row of the ResultSet into a bean with the Class given in the constructor.
BeanListHandler: 
ColumnHandler：Object
ColumnListHandler: 取一列 返回 List<Object>
ScalarHanlder:  查询后只有一个结果的时候使用。
MapHandler: 列名：列值
MapListHandler: List<Map<String, Object>>
```

>Bean 必须由 空构造函数， setter/getter


# 连接池

以 JDBC 为例子，用池子管理 Connection

连接池规范接口：`javas.sql.DataSource`

常见连接池：DBCP,C3PO

# DBCP

>Tomcat JavaWeb 服务器

需要使用的 jar 包：

```
commons-dbcp2-2.5.0.jar
commons-pool2-2.5.0.jar
```

基本使用：

```
BasicDataSource basicDataSource = new BasicDataSource();
basicDataSource.setDriverClassName("com.mysql.jdbc.Driver");
basicDataSource.setUrl("jdbc:mysql://localhost:3306/jdbc_ex?usee");
basicDataSource.setUsername("root");
basicDataSource.setPassword(
try {
    Connection connection = basicDataSource.getConnection();
} catch (SQLException e) {
    e.printStackTrace();
    throw new RuntimeException("database connect fail");
}
```

## 常见配置项目

必须项：

```
driverClassName
url
username
password
```

基本项（扩展）:

```
maxActive
minidle
maxidle
initialSize
```

## DBCPUtils

```
public class JDBCUtils {

    private static BasicDataSource dataSource = new BasicDataSource();

    static {
        // 基本配置
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/jdbc_ex?useSSL=false");
        dataSource.setUsername("root");
        dataSource.setPassword("---");

        // 其他配置
        dataSource.setInitialSize(10); // 初始化连接数
        dataSource.setMinIdle(2); // 最小空闲
        dataSource.setMaxIdle(5); //最大空闲
    }

    public static DataSource getDataSource() {
        return dataSource;
    }
    
}
```


连接池使用--QueryRunner 创建简化：

```
QueryRunner queryRunner = new QueryRunner(DBCPUtils.getDataSource());
```

# 事务

>Transaction

## 命令行模拟事务

先关闭 autocommit

```
start transaction;

//sql

commit; // rollback;
```

## Connect 实现事务

```
Connection connection = JDBCUtils.getConnection();
//关闭自动提交
connection.setAutoCommit(false);

//。。。sql

connection.commit();
//or
connection.rollback();
```

## 事务特性

1. A 原子性，事务中包含的逻辑不可分割
2. C 一致性，数据的完整性
3. I 隔离性，事务在执行期间不能收到其他事务的影响
4. D 持久性，事务执行成功后数据持久保存

## 事务安全隐含

### 读：

- 脏读：一个事务读到另一个事务未提交的数据
- 不可重读：一个事务读到另一个事务已提交的数据，导致两次查询结果不一致
- 幻读：一个事务读到另一个事务已提交的插入的数据，导致多次查询结果不一致

### 写：

- 丢失更新

处理丢失更新问题：

1. 悲观锁：认为一定为出现数据丢失

```
// for update 数据库的锁机制，排他锁
select * from account for update
```

>在查询的时候加入 for update

2. 乐观锁

>要求程序员自己控制

数据库中额外加个字段(version).

事务在提交的时候，比较数据中的 version 和自己基于修改的 version 版本是否一致。

### 隔离级别：

- Read Uncommited, 读未提交，引发脏读
- Read Committed，读已提交；引发 不可重读
- Repeatable Read，可重复读；mysql 默认隔离级别，引发 幻读
- Serializable，可串行化；安全，但是性能不好，需要等另一个事务结束才能继续事务

效率:

```
读未提交 > 读已提交 > 可重复读 > 可串行化
```

拦截程度：

```
读未提交 < 读已提交 < 可重复读 < 可串行化
```









