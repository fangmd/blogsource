---
title: Django 命令记录
date: 2016-10-1 13:18:12
tags: [Django]
category: Python

---

# Create project 创建工程

    django-admin startproject [project name]

# run server 运行工程

    python3 manage.py runserver <port>

# sync db

>过时？

    python3 manage.py syncdb

# create apps 创建应用

    python3 manage.py startapp [app name]

创建后需要在 `settings.py` 中注册： 比如创建了 app:blog，注册：

    INSTALLED_APPS = [
        ...
        # my apps
        'blog.apps.BlogConfig',
    ]

# create necessary database tables based on INSTALLED_APPS setting 更新数据库
创建INSTALLED_APPS中默认的一些app需要的数据库表

The migrate command looks at the INSTALLED_APPS setting and creates any necessary database tables according to the database settings in your mysite/settings.py file and the database migrations shipped with the app (we’ll cover those later).

    python3 manage.py makemigrations

    python3 manage.py migreate

<!--more-->

# 创建超级管理员

    python3 manage.py createsuperuser

# update db after modified Model
在创建了某个app的Model后执行下面的命令，让db更新（需要先在INSTALLED_APPS中注册APP）

    python3 manage.py makemigrations [app name] # 指定更新某个app

上面的代码会生成 migrations 文件，里面是创建数据表等相关的命令语句,执行下面的语句实际执行语句，创建需要的数据库数据

    python3 manage.py migrate

# shell

    python manage.py shell
