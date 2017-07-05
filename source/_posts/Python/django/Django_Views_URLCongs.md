---
title: Django Views URLconfs
date: 2016-10-03 13:18:12
tags: [Django, Views]
category: Python

---

# OverView

django 中使用 URLConfs，管理 url,这个模块是纯 Python 代码的，它将 url 模版和 Python 方法联系在一起。

# How Django processes a request

当一个用户访问 django 中的一个网站的时候，会有下面的算法决定哪个Python代码会执行：

1. django 决定根 URLConfs 执行, URLConfs 文件的指定，在 `settings.py` 中指定 `ROOT_URLCONF`. 但是如果HTTPREQUEST中有指定 `urlconf` 属性的话，这个属性就会替代文件中指定的文件。
2. django 加载 Python 的 Module 并且寻找 `urlpatterns`，它是 `django.conf.urls.url()` 的实例
3. django 运行每个 URL pattern，直到某个正则表达式符合当前用户访问的地址。
4. 找到 urlpatterns 中的匹配项后， django 导入并执行view中指定的方法。view会传递下面的参数：
    1. 一个`HttpRequest`的实例
    2. 。。
    3. kwargs 指定的额外参数

5. 如果没有正则表达式能匹配地址，或者在 view 执行过程中发生错误，django 会调用 error-handing

<!--more-->

# Example

Here's a sample URLconf:

    from django.conf.urls import url

    from . import views

    urlpatterns = [
        url(r'^articles/2003/$', views.special_case_2003),
        url(r'^articles/([0-9]{4})/$', views.year_archive),
        url(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
        url(r'^articles/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.article_detail),
    ]

Notes:

- 在圆括号中定义要捕获的url
- 这里不需要加第一个`/`
- `r`是可选的但是推荐使用

请求例子讲解：

- 请求`/articles/2005/03/`匹配第三项，执行方法`views.moth_archive(request, '2005', '03')`
- 请求`/articles/2005/3/`没有匹配项
- 请求`/articles/2003/`匹配第一项，执行`views.special_case_2003`
- 请求`／articles／2003`没有匹配项

# Named groups 命名组

it’s possible to use named regular-expression groups to capture URL bits and pass them as keyword arguments to a view.

语法:`?P<name>pattern`，给正则表达式命名。

    from django.conf.urls import url

    from . import views

    urlpatterns = [
        url(r'^articles/2003/$', views.special_case_2003),
        url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
        url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
        url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<day>[0-9]{2})/$', views.article_detail),
    ]

这个例子和上面的例子基本一样。不过，这里的参数变成了关键字参数而不是位置参数。

- 如果请求`/articles/2005/03/`,或调用`views.month_archive(request, year='2005', month='03')` instead of `views.month_archive(request, '2005', '03').`

在实际应用中，URLconf会更加清晰，也不会参数参数顺序混乱的问题，也可以在views的方法中重新对参数排序。

## 匹配和分组的算法

下面是URLconf解析器的算法，针对正则表达式中命名组和非命名组：

1. 如果表达式中有命名参数，就会忽略未命名的参数
2. 否则，将以位置参数传递所有的未命名参数

# What the URLconf searches against

URLconf在处理请求url的时候，将其认为是python中的string，不会包含GET,POST，域名等内容。

即使用get，post访问的时候会调用同一个Views的方法：

    https://www.example.com/myapp/

    https://www.example.com/myapp/?page=3

# Captured arguments are always strings

捕获的参数传递给views的时候也是普通的string

# Specifying defaults for view arguments

定义默认参数

例子：

    # URLconf
    from django.conf.urls import url

    from . import views

    urlpatterns = [
        url(r'^blog/$', views.page),
        url(r'^blog/page(?P<num>[0-9]+)/$', views.page),
    ]

    # View (in blog/views.py)
    def page(request, num="1"):
        # Output the appropriate page of blog entries, according to num.
        ...

# Performance 性能

urlpatterns在第一次被访问的时候被编译，这使得系统很快。

## Syntax of the urlpatterns variable

urlpatterns should be a Python list of url() instances.

## Error handling

使用四个值来设置error-handling view

- handler400 – See django.conf.urls.handler400.
- handler403 – See django.conf.urls.handler403.
- handler404 – See django.conf.urls.handler404.
- handler500 – See django.conf.urls.handler500.

## 包含其他 URLconfs

例子：

    from django.conf.urls import include, url

    urlpatterns = [
        # ... snip ...
        url(r'^community/', include('django_website.aggregator.urls')),
        url(r'^contact/', include('django_website.contact.urls')),
        # ... snip ...
    ]

**注意**这个例子中正则表达式中没有`$`，当django遇到`include()`的时候，他会去掉匹配的部分，将剩余的url传递给`include()`中的URLconf处理。

另一种情况是增加额外的url patters：

    from django.conf.urls import include, url

    from apps.main import views as main_views
    from credit import views as credit_views

    extra_patterns = [
        url(r'^reports/$', credit_views.report),
        url(r'^reports/(?P<id>[0-9]+)/$', credit_views.report),
        url(r'^charge/$', credit_views.charge),
    ]

    urlpatterns = [
        url(r'^$', main_views.homepage),
        url(r'^help/', include('apps.help.urls')),
        url(r'^credit/', include(extra_patterns)),
    ]

下面的方法用于去除URLconf冗余：

    from django.conf.urls import url
    from . import views

    urlpatterns = [
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/history/$', views.history),
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/edit/$', views.edit),
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/discuss/$', views.discuss),
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/permissions/$', views.permissions),
    ]

to：

    from django.conf.urls import include, url
    from . import views

    urlpatterns = [
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/', include([
            url(r'^history/$', views.history),
            url(r'^edit/$', views.edit),
            url(r'^discuss/$', views.discuss),
            url(r'^permissions/$', views.permissions),
        ])),
    ]

# captured parameters

`include`进来的url可以获取父URLconf获取到的参数。

# Nested arguments

正则表达式允许嵌套参数，django会将内部参数传递给views

e.g.

    from django.conf.urls import url

    urlpatterns = [
        url(r'blog/(page-(\d+)/)?$', blog_articles),                  # bad
        url(r'comments/(?:page-(?P<page_number>\d+)/)?$', comments),  # good
    ]

# Passing extra options to view functions

可以将额外参数以字典的形式在url第三个参数中传入

e.g.

    from django.conf.urls import url
    from . import views

    urlpatterns = [
        url(r'^blog/(?P<year>[0-9]{4})/$', views.year_archive, {'foo': 'bar'}),
    ]

如果字典中的参数key和url中的name一样，django会使用字典中的值。

# Passing extra options to include()

e.g.

set one:

    # main.py
    from django.conf.urls import include, url

    urlpatterns = [
        url(r'^blog/', include('inner'), {'blogid': 3}),
    ]

    # inner.py
    from django.conf.urls import url
    from mysite import views

    urlpatterns = [
        url(r'^archive/$', views.archive),
        url(r'^about/$', views.about),
    ]

set two:

    # main.py
    from django.conf.urls import include, url
    from mysite import views

    urlpatterns = [
        url(r'^blog/', include('inner')),
    ]

    # inner.py
    from django.conf.urls import url

    urlpatterns = [
        url(r'^archive/$', views.archive, {'blogid': 3}),
        url(r'^about/$', views.about, {'blogid': 3}),
    ]

# Reverse resolution of URLs url反向解析

在django项目中常有的一个需求是获取url的最终形式，用于嵌入到网页或者展示给用户，或者用于服务器的导航和重定向。

django提供不同的工具，在不同的地方使用来获取url：

1. In templates: Using the url template tag.
2. In Python code: Using the reverse() function.
3. In higher level code related to handling of URLs of Django model instances: The get_absolute_url() method.

# Examples

    from django.conf.urls import url

    from . import views

    urlpatterns = [
        #...
        url(r'^articles/([0-9]{4})/$', views.year_archive, name='news-year-archive'),
        #...
    ]

template：

    <a href="{% url 'news-year-archive' 2012 %}">2012 Archive</a>
    {# Or with the year in a template context variable: #}
    <ul>
    {% for yearvar in year_list %}
    <li><a href="{% url 'news-year-archive' yearvar %}">{{ yearvar }} Archive</a></li>
    {% endfor %}
    </ul>

Python code：

    from django.urls import reverse
    from django.http import HttpResponseRedirect

    def redirect_to_year(request):
        # ...
        year = 2006
        # ...
        return HttpResponseRedirect(reverse('news-year-archive', args=(year,)))

在某些场景中只有name是不足以获取url的，请看下一章节

# Naming URL patterns

对url patterns命名，需要防止冲突，建议在前面加app前缀

# URL namespaces


## Introduction

url namespaces 帮助你获取 url 即使不用的 app 使用了相同的 url name。

### application namespaces

### instance namespace

使用 `:` 定义命名空间

    polls:index

定义命名空间： `namespcae="blog"`




。。。。
