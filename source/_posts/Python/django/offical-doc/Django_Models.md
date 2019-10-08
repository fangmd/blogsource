---
title: Django Models
date: 2016-10-03 13:18:12
tags: [Django, Models]
category: Python

---

模型是数据的表现形式，包含了存储数据必要的字段和行为。通常一个Models对应数据库中的一张表。

基本：

1. 每个Models都是` django.db.models.Model`的子类
2. 模型的每个属性都代表了数据库中的一个字段
3. django提供了一套自动生成的用于访问数据库中api

# Models基本创建

    from django.db import models

    class Person(models.Model):
        first_name = models.CharField(max_length=30)
        last_name = models.CharField(max_length=30)

这里的每个属性都对应数据库中的一个字段，默认会创建一个id。

创建上面的Models的sql：

    CREATE TABLE myapp_person (
        "id" serial NOT NULL PRIMARY KEY,
        "first_name" varchar(30) NOT NULL,
        "last_name" varchar(30) NOT NULL
    );

# 使用Models

在使用前要告诉django你定义了这些models，需要在`INSTANCE_APP`中注册Models所在的app，然后执行：

    python3 manage.py makemigrations

生成sql脚本

    python3 manage.py migrate

<!--more-->

# Fields 字段

注意：模型的属性名不能与模型的api冲突，比如：`save`,`clean`,`delete`

由 model 类的属性决定

# Fields Type

模型中的每个字段都是`Field`子类的某个实例，django根据字段的类型确定一下信息：

1. 数据库创建时候字段的数据类型（INTEGE,TEXT,VARCHAR）
2. HTML表单的渲染（e.g. <input type="text">, <select>)）
3. 数据最低限度的验证

# Fields options

每个字段都有很多可选的参数

- `null=True`:表示数据库会将用null存储数据空值，默认是false
- `blank=True`:表示该字段可以不填，默认是false
- `choices=[iterable]`：参数是一个二元组组成的可迭代对象，如果设置了这个选项，默认的表当是一个选择框而不是文本框。元组中的第一个元素是存储在数据库中的值，第二个元素是显示给用户的值（可以使用`get_Foo_display()`方法获取）。

        from django.db import models

        class Person(models.Model):
            SHIRT_SIZES = (
                ('S', 'Small'),
                ('M', 'Medium'),
                ('L', 'Large'),
            )
            name = models.CharField(max_length=60)
            shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)


            >>> p = Person(name="Fred Flintstone", shirt_size="L")
            >>> p.save()
            >>> p.shirt_size
            'L'
            >>> p.get_shirt_size_display()
            'Large'

- `default=[.]`：值可以是一个具体的值也可以是一个可调用的方法对象。
- `help_text=['。']`:提示内容
- `primary_key=[Boolean]`:if True,表示这个属性是Model的主键，如果没有设置这个，django会为model创建一个默认的`IntegerField`作为主键。

    The primary key field is read-only. If you change the value of the primary key on an existing object and then save it, a new object will be created alongside the old one.

- `unique=[Boolean]`:if True，表示该字段唯一

## Automatic primary key Fields

默认django为所有的models指定 主键：

    id = models.AutoField(primary_key=True)

## Verbose field name 字段的自述

 除了`ForeignKey`, `ManyToManyField` and `OneToOneField`,其他所有的字段都有一个属性：a verbose name

 eg：

    first_name = models.CharField("person's first name", max_length=30)

`ForeignKey`, `ManyToManyField` and `OneToOneField`都要求第一个参数是模型的类，所以必须指定键：

    poll = models.ForeignKey(
        Poll,
        on_delete=models.CASCADE,
        verbose_name="the related poll",
    )
    sites = models.ManyToManyField(Site, verbose_name="list of sites")
    place = models.OneToOneField(
        Place,
        on_delete=models.CASCADE,
        verbose_name="related place",
    )

# RelationShips

## Many to one RelationShips
` django.db.models.ForeignKey`

`ForeignKey`需要一个位置参数，与本model相管理的model class

eg:

    from django.db import models

    class Manufacturer(models.Model):
        # ...
        pass

    class Car(models.Model):
        manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
        # ...

支持递归关系
## many to many RelationShips

ManyToManyField requires a positional argument: the class to which the model is related.

    from django.db import models

    class Topping(models.Model):
        # ...
        pass

    class Pizza(models.Model):
        # ...
        toppings = models.ManyToManyField(Topping)

支持递归关系

## Extra fields on many-to-many relationships 多对多的其他参数

需要将数据关联到两个模型的关系上。

`through`指向中介模型

下面是一个音乐小组实例：

    from django.db import models

    class Person(models.Model):
        name = models.CharField(max_length=128)

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Group(models.Model):
        name = models.CharField(max_length=128)
        members = models.ManyToManyField(Person, through='Membership')

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Membership(models.Model):
        person = models.ForeignKey(Person, on_delete=models.CASCADE)
        group = models.ForeignKey(Group, on_delete=models.CASCADE)
        date_joined = models.DateField()
        invite_reason = models.CharField(max_length=64)

。。。

## one to one RelationShips

## Models across files

    from django.db import models
    from geography.models import ZipCode

    class Restaurant(models.Model):
    # ...
    zip_code = models.ForeignKey(
        ZipCode,
        on_delete=models.SET_NULL,
        blank=True,
        null=True,
    )

## Field name restrictions 字段名限制

1. 不能是python的保留字段
2. 名称中的不允许有连续下滑线

## Custom field types

# Meta options

- abstract: true 表示抽象类
- app_label：

        app_label = 'myapp'

    如果一个 model 在 app 之外创建的，就需要指定其相应的 app。
- base_manager_name
- db_table:

        db_table = 'music_album'

    指定表名

- db_tablespace
- ordering:

    default: - 表示降序

        ordering = ['-order_date']

    设置按多个字段排序：
        ordering = ['-pub_date', 'author']


# Model attributes

# Model methods

### __str__() (Python 3)

A Python “magic method” that returns a unicode “representation” of any object.

返回model的表现方式

### get_absolute_url()

## Overriding predefined model methods

覆盖一些预定义的方法：

比如save：

    from django.db import models

    class Blog(models.Model):
        name = models.CharField(max_length=100)
        tagline = models.TextField()

        def save(self, *args, **kwargs):
            do_something()
            super(Blog, self).save(*args, **kwargs) # Call the "real" save() method.
            do_something_else()


    from django.db import models

    class Blog(models.Model):
        name = models.CharField(max_length=100)
        tagline = models.TextField()

        def save(self, *args, **kwargs):
            if self.name == "Yoko Ono's blog":
                return # Yoko shall never have her own blog!
            else:
                super(Blog, self).save(*args, **kwargs) # Call the "real" save() method.

## executing custom sql

# Model inheritance

有3种情况的继承：

1. 只希望父类持有多个子类必须的属性，父类不需要实例化，可以使用 抽象基类
2. 继承一个已经存在的模型，并且希望建立新的数据库表，使用 多表继承  Multi-table inheritance
3. 只想改变模型的方法，不改变属性，可以使用 代理模型

## Abstract base class

创建一个抽象的父类：（创建一个内部class，并指定属性abstract为ture）

    from django.db import models

    class CommonInfo(models.Model):
        name = models.CharField(max_length=100)
        age = models.PositiveIntegerField()

        class Meta:
            abstract = True

    class Student(CommonInfo):
        home_group = models.CharField(max_length=5)

`CommonInfo`是一个抽象类，它不会创建数据库表也不会被实例化。它的不同的子类还是会创建各自的数据库表。

### Meta Inheritance

内部类 `Meta`也可以被继承和覆盖：

    from django.db import models

    class CommonInfo(models.Model):
        # ...
        class Meta:
            abstract = True
            ordering = ['name']

    class Student(CommonInfo):
        # ...
        class Meta(CommonInfo.Meta):
            db_table = 'student_info'

对于继承抽象类的类，django会自动将继承的内部类`Meta`中的属性`abstract = False`，如果需要将子类也变成抽象类，需要用户自己重写子类`Meta`

如果`Meta`设置了抽象属性，一些其他的属性会失效，比如`db_table`属性

### Be careful with related_name and related_query_name
