---
title: MongoDb
date: 2018-07-16 13:18:12
tags: [Database]
category: Database

---

# Macos

Install

```
brew install mongodb
```

配置文件地址：

```
mongod --config /usr/local/etc/mongod.conf
```

运行 db: 

```
# 创建默认的数据库路径
mkdir -p /data/db

# 启动
sudo mongod

sudo mongod --dbpath /Users/double/data/db
```

>注意：如果你的数据库目录不是/data/db，可以通过 --dbpath 来指定。

启动 db 并且执行 db 数据库路径

```
sudo mongod --dbpath /Users/double/data/db
```


