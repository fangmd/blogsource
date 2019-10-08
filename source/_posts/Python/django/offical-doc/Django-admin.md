---
title: Django-admin
date: 2016-12-19 13:18:12
tags: [Django, admin]
category: Python

---

>From Git Book

# django.contrib 包

Django自带很多优秀的附加组件，它们都存在于 django.contrib 包里。

# 启动 admin
创建的工程默认已经开启了

# 创建 admin 超级管理员

    python3 manage.py createsuperuser

# 将创建好的 Model 加入到 admin 中管理

在 admin.py 中：调用下面的方法注册，参数是 Model

    admin.site.register(Publisher)

# 设置 model 的字段可选

model 的 Field 属性添加下面的参数

    blank=True

你想允许一个日期型（DateField、TimeField、DateTimeField）或数字型（IntegerField、DecimalField、FloatField）字段为空，你需要使用 null=True * 和* blank=True。

# 自定义字段的标签

model 的 Field 使用 verbose_name 属性更改标签名

# 自定义列表

定义显示的名称：model 的 __str__

    def __str__(self):
        return [name]

自定义列表：admin.py

    class AuthorAdmin(admin.ModelAdmin):
        list_display = ('first_name', 'last_name', 'email')

    admin.site.register(Author, AuthorAdmin)

添加一个快速查询栏:

    class AuthorAdmin(admin.ModelAdmin):
        list_display = ('first_name', 'last_name', 'email')
        search_fields = ('first_name', 'last_name')

添加过滤：

    class BookAdmin(admin.ModelAdmin):
        list_display = ('title', 'publisher', 'publication_date')
        list_filter = ('publication_date',)

过滤二：

    date_hierarchy = 'publication_date'

设置排序：

    ordering = ('-publication_date',)

# 自定义编辑表单
同样使用 ModelAdmin 子类管理

定义顺序：

    fields = ('title', 'authors', 'publisher', 'publication_date')

隐藏字段：

    fields = ('title', 'authors', 'publisher')

多对多的多选框处理：

     filter_horizontal = ('authors',)
     或者
     filter_vertical = ('authors',)

ForeignKey 字段更好的表现显示：

    raw_id_fields = ('publisher',)

# 用户、用户组和权限

因为你是用超级用户登录的，你可以创建，编辑和删除任何对像。 然而，不同的环境要求有不同的权限，系统不允许所有人都是超级用户。 管理工具有一个用户权限系统，通过它你可以根据用户的需要来指定他们的权限，从而达到部分访问系统的目的。

1. 活动标志，它用来控制用户是否已经激活。 如果一个用户帐号的这个标记是关闭状态，而用户又尝试用它登录时，即使密码正确，他也无法登录系统。
2. 成员标志，它用来控制这个用户是否可以登录管理界面（即：这个用户是不是你们组织里的成员） 由于用户系统可以被用于控制公众页面（即：非管理页面）的访问权限（详见第十四章），这个标志可用来区分公众用户和管理用户。
3. 超级用户标志，它赋予用户在管理界面中添加、修改和删除任何项目的权限。 如果一个用户帐号有这个标志，那么所有权限设置（即使没有）都会被忽略。
