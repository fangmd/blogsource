---
title: MongoDb
date: 2018-07-16 13:18:12
tags: [Database]
category: Database

---

# Macos

Install

```
sudo brew install mongodb
```

配置文件地址：

```
mongod --config /usr/local/etc/mongod.conf
```

运行 db: 

```
# 创建默认的数据库路径
sudo mkdir -p /data/db

# 启动
sudo mongod
```

>注意：如果你的数据库目录不是/data/db，可以通过 --dbpath 来指定。



