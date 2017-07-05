---
title: Django 报错记录
date: 2016-11-21 13:18:12
tags: [Django, 报错记录]
category: Python

---


# NoReverseMatch

    Reverse for 'index' with arguments '()' and keyword arguments '{}' not found. 0 pattern(s) tried: []

原因：URLconf 设置错误

错误例子：

    主urls.py中的
    url(r'^$', include('blog.urls')),
    修改成
    url(r'^', include('blog.urls')),就可以了。

$判断字符串结束，所以后面的子url她没法解析了
