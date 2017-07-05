---
title: Django official Demo
date: 2016-09-16 13:18:12
tags: [Django, 官方文档]
category: Python

---


# Create a project
创建一个项目：

    django-admin startproject mysite

    mysite/
        manage.py
        mysite/
            __init__.py
            settings.py
            urls.py
            wsgi.py

- mysite:root directory ,container project，这个目录作用不大，可以重新命名
- manage.py:是一个命令行工具，通过他来和项目交互，在django-admin.py,manage.py中查看细节
- mysite/mysite/:actual Python package for project.实际项目的包名
- mysite/__init__.py:An empty file that tells Python that this directory should be considered a Python package.空文件，告诉Python该目录是一个Python包
- mysite/settings.py:Settings/configuration for this Django project.
- mysite/urls.py:urls，URL声明，一份由django驱动的网站目录
- mysite/wsgi.py:一个WSGI兼容的web服务器入口


# The development server

Change into the outer mysite directory.

then:

    python3 manage.py runserver

start Django development server.

now wo can visit http://127.0.0.1:8000/

<!--more-->

# Creating the Polls app

创建一个app和manage.py平级

    python3 manage.py startapp polls

create a directory polls, which will house the poll application.

# write your first view


    polls/views.py
    from django.http import HttpResponse


    def index(request):
        return HttpResponse("Hello, world. You're at the polls index.")

then map it to URL:

    polls/urls.py
    from django.conf.urls import url

    from . import views

    urlpatterns = [
        url(r'^$', views.index, name='index'),
    ]

point the root URLconf at the pools.urls module, in mysite/urls.py:

    mysite/urls.py
    from django.conf.urls import include, url
    from django.contrib import admin

    urlpatterns = [
        url(r'^polls/', include('polls.urls')),
        url(r'^admin/', admin.site.urls),
    ]

`include()` function allows referencing other URLconfs.

Note:doesn't have `$`

start server:

    python3 manage.py runserver

`url()`function 接受四个参数，两个必须：regex，view，两个可选：kwargs，name

## url()
- regex
- view
- kwargs
- name


## 上面的内容整理

1. 创建一个工程
2. 创建一个app
3. 为app创建一个View
4. 为View创建urlyings
5. 将app的URLConf在工程的URL中声明

# Database setup

`mysite/settings.py`这个文件中包含了Django设置的模块级设置

    # Database
    # https://docs.djangoproject.com/en/1.10/ref/settings/#databases

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        }
    }

`ENGINE`指定了数据库的类型

`NAME`数据库的名字，如果是使用sqlite数据库那就是数据库的文件的绝对路径

    TIME_ZONE = 'UTC'

`TIME_ZONE`时区，默认是美国

    # Application definition

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]

每个django工程中都有上面的几个app

上面的app也会在数据库中创建需要的表，使用下面的命令，创建他们需要的数据库：

    python manage.py migrate


# Creating models

    polls/models.py
    from django.db import models


    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')


    class Choice(models.Model):
        question = models.ForeignKey(Question, on_delete=models.CASCADE)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)

Each model is subclasses `djanago.db.models.Model`

每个Model都有一个成员变量：`Field`, 比如`CharField`，`DateTimeField`,表明数据类型

成员变量的变量名将会作为数据库中的字段名保存。

一些成员变量需要参数：比如`CharField`,`max_length`可以用于数据库，和输入验证。

默认参数：`default=0`

最后：关联两个类，表，`ForeignKey`,supports all the common database relationships:many-to-one,many-to-mant,one-to-one

# Activating models

    mysite/settings.py
    INSTALLED_APPS = [
        'polls.apps.PollsConfig',
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]

run:

    python3 manage.py makemigrations polls

Bu running `makemigrations`, tell Django that models is changed.

    python3 manage.py migrate


## 上面数据相关操作整理
1. 修改Model
2. python3 manage.py makemigrations [app name]
3. python3 manage.py migrate


# Playing with the API

invoke Python shell:

    python3 manage.py shell

使用`database api`操作数据mm

# Creating an admin user

    python3 manage.py createsuperuser

# start the development server

    python3 manage.py runserver

# Make the poll app modifiable in the admin

    polls/admin.py
    from django.contrib import admin

    from .models import Question

    admin.site.register(Question)


## 上面内容整理

1. 创建一个数据库，并关联到项目中
2. 创建app的model
3. 更新数据库
4. 使用 database api 操作数据库
5. 创建Admin用户
6. 将app的Model注册到Admin中，让admin用户可以网页中直接修改Model数据

# part3
## Overview

一个view就是Web的一个页面

使用url模版生成url：`/newsarchive/<year>/<month>/`

让url和view关联：`URLconfs`

## Writing more views

add more views:

    polls/views.py
    def detail(request, question_id):
        return HttpResponse("You're looking at question %s." % question_id)

    def results(request, question_id):
        response = "You're looking at the results of question %s."
        return HttpResponse(response % question_id)

    def vote(request, question_id):
        return HttpResponse("You're voting on question %s." % question_id)

add views into `polls.urls`module:


    polls/urls.py
    from django.conf.urls import url

    from . import views

    urlpatterns = [
        # ex: /polls/
        url(r'^$', views.index, name='index'),
        # ex: /polls/5/
        url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
        # ex: /polls/5/results/
        url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
        # ex: /polls/5/vote/
        url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
    ]

## Write views that actually do something

每个view响应方法做两件事：

1. 返回`HttpResponse`对象，包含了响应的内容，或者`Http404`

use Django's own database API:

    polls/views.py
    from django.http import HttpResponse

    from .models import Question


    def index(request):
        latest_question_list = Question.objects.order_by('-pub_date')[:5]
        output = ', '.join([q.question_text for q in latest_question_list])
        return HttpResponse(output)

    # Leave the rest of the views (detail, results, vote) unchanged
...

# part 4

## write a simple form

update detail.html


## Use generic views: Less code is better

1. Convert the URLconf.
2. Delete some of the old, unneeded views.
3. Introduce new views based on Django’s generic views.

### Amend URLconf

### Amend Views

# part 5

## Introducing automated testing

## Writing our first test

# part 6

## Customize your app’s look and feel¶

# part 7
## Customize the admin form¶

## Adding related objects

## Customize the admin change list

# Advanced tutorial: How to write reusable apps
