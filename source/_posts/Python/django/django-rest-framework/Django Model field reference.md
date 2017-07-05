---
title: Django Model Field reference
date: 2016-11-10 13:18:12
tags: [Django, Models, Field]
category: Python

---


# Field options

## null

`Field.null`

如果 True django 会用 NULL 存储空值。默认 False.

>If True, Django will store empty values as NULL in the database. Default is False.

If you want to accept null values with BooleanField, use NullBooleanField instead.

<!--more-->

## blank

`Field.blank`

参数是否能够为空。 True：表示可以为空，默认 False
>If True, the field is allowed to be blank. Default is False.

## choices

`Field.choices`


# Field types

## AutoField

## BigIntegerField

## BinaryField

## BooleanField

## CharField

## CommaSeparatedIntegerField

## DateField

时间，代表 python 中的`datetime.date`实例

特殊参数：

1. DateField.auto_now ： 自动设置时间，在创建的时候值会更新
2. DateField.auto_now_add ：设置时间在对象更新的时候也更新

上面两个参数其中一个设置为 True，同时会设置`editable=False`,`blank=True`

## DateTimeField

对应 Python 中的 ` datetime.datetime` 对象

参数和 `DateField` 一致

## TextField

长字符串使用

对应 html 的输入控件 `Textarea`

如果设置了 `max_length` 参数，这个值会反映到 `Textarea` 上，但是在数据库层面上不会被检查。

## CharField

短字符串使用

>A string field, for small- to large-sized strings.

对应 html 的输入控件 `TextInput`

特殊属性：

1. CharField.max_length


- [https://docs.djangoproject.com/en/1.9/ref/models/fields/](https://docs.djangoproject.com/en/1.9/ref/models/fields/)
