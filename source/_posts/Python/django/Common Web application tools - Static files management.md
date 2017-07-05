---
title: Common Web application tools - Static files management
date: 2016-11-21 13:18:12
tags: [django, bootstrapI]
category: Python

---


`django.contrib.staticfiles` 收集所有的 app 的静态文件到一个地方，方便程序调用

# Settings

`settings.py`

- STATIC_ROOT:设置 collectstatic 将会收集静态文件地址的绝对路径地址
- STATIC_URL:定义静态文件的 url

    Example: "/static/" or "http://static.example.com/"

- STATICFILES_DIRS：


    exa：

            STATICFILES_DIRS = [
            "/home/special.polls.com/polls/static",
            "/home/polls.com/polls/static",
            "/opt/webfiles/common",
        ]

- STATICFILES_STORAGE
- STATICFILES_FINDERS



。。。
