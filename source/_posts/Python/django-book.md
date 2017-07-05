---
title: Django Book 记录
date: 2016-12-18 13:18:12
tags: [Django, Django-Book]
category: Python

---

>http://djangobook.com/


# 空项目目录结构介绍

创建一个项目：
```
django-admin startproject mysite
```


    mysite/
        manage.py
        mysite/M
            __init__.py
            settings.py
            urls.py
            wsgi.py

- mysite:root directory ,container project，这个目录作用不大，可以重新命名
- manage.py:是一个命令行工具，通过他来和项目交互，在django-admin.py,manage.py中查看细节
- mysite/mysite/:actual Python package for project.实际项目的包名
- mysite/__init__.py:An empty file that tells Python that this directory should be considered a Python package.空文件，告诉Python该目录是一个Python包
- mysite/settings.py:Settings/configuration for this Django project.
- mysite/urls.py:urls，URL声明，一份由 django 驱动的网站目录
- mysite/wsgi.py:一个WSGI兼容的web服务器入口

# 让局域网内的人也访问自己电脑上的临时服务器

    python manage.py runserver 0.0.0.0:8000

表示服务器监听任意的网络接口

其他人使用你的 本地ip 就可以反问了：比如 你的本地ip：129.168.1.103，别人就可以通过下面的网络访问

    http://192.168.1.103:8000/

# 关于 url 反斜杠

>受配置文件setting中APPEND_SLASH项控制

如果你是喜欢所有URL都以’/’结尾的人（Django开发者的偏爱），那么你只需要在每个URL后添加斜杠，并且设置”APPEND_SLASH”为”True”. 如果不喜欢URL以斜杠结尾或者根据每个URL来决定，那么需要设置”APPEND_SLASH”为”False”,并且根据你自己的意愿来添加结尾斜杠/在URL模式后.

# How Django processes a request

When a user requests a page from your Django-powered site, this is the algorithm the system follows to determine which Python code to execute:

1. Django determines the root URLconf module to use. Ordinarily, this is the value of the ROOT_URLCONF setting, but if the incoming HttpRequest object has a urlconf attribute (set by middleware), its value will be used in place of the ROOT_URLCONF setting.
2. Django loads that Python module and looks for the variable urlpatterns. This should be a Python list of django.conf.urls.url() instances.
3. Django runs through each URL pattern, in order, and stops at the first one that matches the requested URL.
Once one of the regexes matches, Django imports and calls the given view, which is a simple Python function (or a class-based view). The view gets passed the following arguments:
An instance of HttpRequest.
4. If the matched regular expression returned no named groups, then the matches from the regular expression are provided as positional arguments.
5. The keyword arguments are made up of any named groups matched by the regular expression, overridden by any arguments specified in the optional kwargs argument to django.conf.urls.url().
If no regex matches, or if an exception is raised during any point in this process, Django invokes an appropriate error-handling view. See Error handling below.

# 模版
模板系统在变量名中遇到点时，按照以下顺序尝试进行查找：

1. 字典类型查找 （比如 foo["bar"] )
2. 属性查找 (比如 foo.bar )
3. 方法调用 （比如 foo.bar() )
4. 列表类型索引查找 (比如 foo[bar] )

# 元祖后面的逗号

Python 要求单元素元组中必须使用逗号，以此消除与圆括号表达式之间的歧义。

    # Comma correctly in place.
    TEMPLATE_DIRS = (
        '/home/django/mysite/templates',
    )

# Model 代码 增删改查

以 Model：为例子

    class Publisher(models.Model):
        name = models.CharField(max_length=30)
        address = models.CharField(max_length=50)
        city = models.CharField(max_length=60)
        state_province = models.CharField(max_length=30)
        country = models.CharField(max_length=50)
        website = models.URLField()

        def __str__(self):
            return self.name

## 增

1. 创建 Model 对象
2. 调用 model.save()

example：

    >>> p = Publisher(name='Apress',
    ...         address='2855 Telegraph Ave.',
    ...         city='Berkeley',
    ...         state_province='CA',
    ...         country='U.S.A.',
    ...         website='http://www.apress.com/')

    >>> p.save()

## 删

### 删除一个对象

    >>> p.delete()

### 删除所有对象

    >>> Publisher.objects.all().delete()

### 删除部分

    >>> Publisher.objects.filter(country='USA').delete()

## 改

### 更新一个对象

1. 在已经获取到 Model 的对象并且它已经对于数据库中的某条数据前提下 修改 model 的属性值
2. 调用 model.save() 更新数据

### 更新所有对象

    >>> Publisher.objects.all().update(country='USA')

update()方法会返回一个整型数值，表示受影响的记录条数。

### 更新多个对象

## 查

## 选择所有

    >>> Publisher.objects.all()

### 过滤数据

    >>> Publisher.objects.filter(name='Apress')
    >>> Publisher.objects.filter(country="U.S.A.", state_province="CA")

模糊查找：查找包含指定字符串

    >>> Publisher.objects.filter(name__contains="press")

icontains 与大小写无关

### 获取当个对象

    >>> Publisher.objects.get(name="Apress")

如果 get 方法查询到多个数据，会报错 `MultipleObjectsReturned: get() returned more than one Publisher --`

如果 get 没有找到数据也，会报错 `DoesNotExist: Publisher matching query does not exist.`

### 数据排序

    >>> Publisher.objects.order_by("name")

多个字段为标准：

    >>> Publisher.objects.order_by("state_province", "address")

逆序：

    >>> Publisher.objects.order_by("-name")

使用 Meta 设置排序方式：

    class Meta:
            ordering = ['name']

### 连锁查询

    >>> Publisher.objects.filter(country="U.S.A.").order_by("-name")

### 限制返回的数据

    >>> Publisher.objects.order_by('name')[0]

使用 切片：

```
>>> Publisher.objects.order_by('name')[0:2]
```

注意，不支持Python的负索引(negative slicing)

# Form

## 验证


# URLconf 高级用法

## URL 导入方式

url 导入方式一普通模式：

url 导入方式二：字符串导入：

```python
urlpatterns = patterns('',
    (r'^hello/$', 'mysite.views.hello' ),
```

配合字符串 url ，使用多个视图前缀：

```python
from django.conf.urls.defaults import *

urlpatterns = patterns('mysite.views',
    (r'^hello/$', 'hello'),
    (r'^time/$', 'current_datetime'),
    (r'^time/plus/(\d{1,2})/$', 'hours_ahead'),
)

urlpatterns += patterns('weblog.views',
    (r'^tag/(\w+)/$', 'tag'),
)
```
## 定义调试模式的特例

```python
if settings.DEBUG:
    urlpatterns += patterns('',
        (r'^debuginfo/$', views.debug),
    )
```
## 使用命名组

语法：
```python
(?P<name>pattern)
```

>切记命名组和非命名组不能同时使用。

## 向视图传入额外参数

```python
urlpatterns = patterns('',
    (r'^foo/$', views.foobar_view, {'template_name': 'template1.html'}),
)

#  views.py
def foobar_view(request, template_name):
    pass

```

## 伪造捕捉到的URLconf值

```python
urlpatterns = patterns('',
    (r'^mydata/birthday/$', views.my_view, {'month': 'jan', 'day': '06'}),
)
```

## 了解捕捉值和额外参数之间的优先级 额外的选项
额外URLconf参数优先于捕捉值

## 使用缺省视图参数
```python
urlpatterns = patterns('',
    (r'^blog/$', views.page),
    (r'^blog/page(?P<num>\d+)/$', views.page),
)
```

# 模板高级进阶

## RequestContext和Context处理器

## 关闭 html自动转意

```html
This will not be escaped: {{ data|safe }}
```

## 设置默认
```html
{{ data|default:"This is a string literal." }}
```

# 数据模型高级进阶

## 访问多对多值(Many-to-Many Values)

```
>>> b = Book.objects.get(id=50)
>>> b.authors.all()
[<Author: Adrian Holovaty>, <Author: Jacob Kaplan-Moss>]
>>> b.authors.filter(first_name='Adrian')
[<Author: Adrian Holovaty>]
>>> b.authors.filter(first_name='Adam')
[]
```

反向查询也可以:

```
>>> a = Author.objects.get(first_name='Adrian', last_name='Holovaty')
>>> a.book_set.all()
[<Book: The Django Book>, <Book: Adrian's Other Book>]
```

# 通用视图

Django内建通用视图可以实现如下功能：

- 完成常用的简单任务： 重定向到另一个页面以及渲染一个指定的模板。
- 显示列表和某个特定对象的详细内容页面。
- 呈现基于日期的数据的年/月/日归档页面，关联的详情页面，最新页面。

## 使用通用视图

例子，呈现静态 “关于” 页面的 URLconf：

```python
from django.conf.urls.defaults import *
from django.views.generic.simple import direct_to_template

urlpatterns = patterns('',
    (r'^about/$', direct_to_template, {
        'template': 'about.html'
    })
)
```




# 会话、用户和注册

## Cookies

cookies 是浏览器为 Web 服务器存储的一小段信息。

### 存取Cookies

HttpRequest 对象中有一个 COOKIES 对象，该对象的行为类似字典：

获取请求中的 cookies
```python
def show_color(request):
    if "favorite_color" in request.COOKIES:
        return HttpResponse("Your favorite color is %s" % request.COOKIES["favorite_color"])
    else:
        return HttpResponse("You don't have a favorite color.")
```

写入 cookies：`set_cookie()`
```python
def set_color(request):
    if "favorite_color" in request.GET:

        # Create an HttpResponse object...
        response = HttpResponse("Your favorite color is now %s" % request.GET["favorite_color"])

        # ... and set a cookie on the response
        response.set_cookie("favorite_color", request.GET["favorite_color"])

        return response

    else:
        return HttpResponse("You didn't give a favorite color.")
```

## Django的 Session 框架

### 打开 Sessions功能

Sessions 功能是通过一个中间件(参见第17章)和一个模型(model)来实现的。

1. 编辑 MIDDLEWARE_CLASSES 配置，确保 MIDDLEWARE_CLASSES 中包含 'django.contrib.sessions.middleware.SessionMiddleware'。
2. 确认 INSTALLED_APPS 中有 'django.contrib.sessions' (如果你是刚打开这个应用，别忘了运行 manage.py syncdb )5

通常建立一个新的工程 Sessions 功能是默认开启的
### 在视图中使用Session

SessionMiddleware 激活后，每个传给视图(view)函数的第一个参数``HttpRequest`` 对象都有一个 session 属性，这是一个字典型的对象。 你可以象用普通字典一样来用它。 

```python
# Set a session value:
request.session["fav_color"] = "blue"

# Get a session value -- this could be called in a different view,
# or many requests later (or both):
fav_color = request.session["fav_color"]

# Clear an item from the session:
del request.session["fav_color"]

# Check if the session has a given key:
if "fav_color" in request.session:
    ...
```

例子：防止用户多次评论的方法

```python
def post_comment(request):
    if request.method != 'POST':
        raise Http404('Only POSTs are allowed')

    if 'comment' not in request.POST:
        raise Http404('Comment not submitted')

    if request.session.get('has_commented', False):
        return HttpResponse("You've already commented.")

    c = comments.Comment(comment=request.POST['comment'])
    c.save()
    request.session['has_commented'] = True
    return HttpResponse('Thanks for your comment!')
```

## 用户与Authentication1

通过session，我们可以在多次浏览器请求中保持数据，接下来的部分就是用session来处理用户登录了。 当然，不能仅凭用户的一面之词，我们就相信，所以我们需要认证。

1. 验证 (认证) 用户是否是他所宣称的用户(一般通过查询数据库验证其用户名和密码)
2. 验证用户是否拥有执行某种操作的 授权 (通常会通过检查一个权限表来确认)

Django 认证/授权 系统会包含以下的部分：

- 用户 : 在网站注册的人
- 权限 : 用于标识用户是否可以执行某种操作的二进制(yes/no)标志
- 组 :一种可以将标记和权限应用于多个用户的常用方法
- Messages : 向用户显示队列式的系统消息的常用方法

### 登录和退出

验证用户:

```python
>>> from django.contrib import auth
>>> user = auth.authenticate(username='john', password='secret')
>>> if user is not None:
...     print "Correct!"
... else:
...     print "Invalid password."
```

登入：

```python
from django.contrib import auth

def login_view(request):
    username = request.POST.get('username', '')
    password = request.POST.get('password', '')
    user = auth.authenticate(username=username, password=password)
    if user is not None and user.is_active:
        # Correct password, and the user is marked "active"
        auth.login(request, user)
        # Redirect to a success page.
        return HttpResponseRedirect("/account/loggedin/")
    else:
        # Show an error page
        return HttpResponseRedirect("/account/invalid/")
```

logout:

```python
from django.contrib import auth

def logout_view(request):
    auth.logout(request)
    # Redirect to a success page.
    return HttpResponseRedirect("/account/loggedout/")
```

>注意，即使用户没有登录， logout() 也不会抛出任何异常。

URLconf:

```python
from django.contrib.auth.views import login, logout

urlpatterns = patterns('',
    # existing patterns here...
    (r'^accounts/login/$',  login),
    (r'^accounts/logout/$', logout),
)
```

### 限制已登录用户的访问

一个简单原始的限制方法是检查 request.user.is_authenticated() ,然后重定向到登陆页面：

或者显示一个出错信息：

作为一个快捷方式, 你可以使用便捷的 login_required 修饰符:

```
from django.contrib.auth.decorators import login_required

@login_required
def my_view(request):
    # ...
```

### 处理注册

```python

from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.http import HttpResponseRedirect
from django.shortcuts import render_to_response

def register(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            new_user = form.save()
            return HttpResponseRedirect("/books/")
    else:
        form = UserCreationForm()
    return render_to_response("registration/register.html", {
        'form': form,
    })
```

template:
```html
{% extends "base.html" %}

{% block title %}Create an account{% endblock %}

{% block content %}
  <h1>Create an account</h1>

  <form action="" method="post">
      {{ form.as_p }}
      <input type="submit" value="Create the account">
  </form>
{% endblock %}
```



## 消息

- 要创建一条新的消息，使用 user.message_set.create(message='message_text') 。
- 要获得/删除消息，使用 user.get_and_delete_messages() ，这会返回一个 Message 对象的列表，并且从队列中删除返回的项。

