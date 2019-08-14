---
title: MySQL学习记录
date: 2018-09-03 13:18:12
tags: [MySQl]
category: Database

---


# Install in mac

clean old: [https://gist.github.com/vitorbritto/0555879fe4414d18569d](https://gist.github.com/vitorbritto/0555879fe4414d18569d)

install:

```
brew install mysql
```

启动：

```
// have launchd start mysql now and restart at login
brew services start mysql
brew services restart mysql

// if you don't want/need a background service you can just run
mysql.server start
```

关闭 mysql:

```
mysqladmin -u root -p shutdown
```

mysql 数据文件夹:

```
/usr/local/var/mysql
```

# 初始化操作 设置 root password

```
mysqladmin -u root password 'xxxxxx'
```

# mysql 配置

默认配置位置：

```
/etc/my.cnf
```



#  mysql 连接方式

```
jdbc:MySQL://[host:port],[host:port].../[database][?参数名1][=参数值1][&参数名2][=参数值2]...

ex:

jdbc:MySQL://localhost:3306/test?user=root&password=&useUnicode=true&characterEncoding=utf8&autoReconnect=true&failOverReadOnly=false 
```

# mysql 终端连接

```
 mysql -u root -p
```

# mysql 用户操作

```
# 创建用户

```

# mysql 数据库 操作

```
# 连接 mysql
mysql -u root -p

其他内容见：sql.md
```

# 问题

## navicat 连接 mysql 失败

原因 root 密码使用 `caching_sha2_password` 加密方式。

修改 root 密码加密方式

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
```





