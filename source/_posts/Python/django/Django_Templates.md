---
title: Django Templates
date: 2016-10-05 13:18:12
tags: [Django, Templates]
category: Python

---


# Support for template engines

在 `settings.py` 中的模版设置在 django1.8 后加入

## Configuration

`settings.py`:

    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [],
            'APP_DIRS': True,
            'OPTIONS': {
                # ... some options here ...
            },
        },
    ]

- BACKEND:dotted Python path to a template engine class implementing Django’s template backend API.
- DIRS: 定义引擎寻找 template 的路径，按顺序寻找
- APP_DIRS：定义引擎是否要在 application 内部寻找 template
- NAME：如果需要定义多个引擎，可以用这个来去命区分

## Usage

`django.template.loader` module 定义了两种方式加载 template

### get_template(template_name, dirs=_dirs_undefined, using=None)[source]

通过 template_name 来获取模版对象

它会使用每个 template engine 知道找到合适的 template，如果没有找到就抛出异常 TemplateDoesNotExist，如果模版的语法发现错误报 TemplateSyntaxError.

如果要定义使用指定的 template engine，可以使用 using 参数，值是 template_name

### select_template(template_name_list, dirs=_dirs_undefined, using=None)[source]

需要参数 template_name_list,从这个集合中寻找 template


### 异常

#### exception TemplateDoesNotExist(msg, tried=None, backend=None, chain=None)[source]

#### exception TemplateSyntaxError(msg)[source]

### use template objects

    Template.render(context=None, request=None)




# The Django template language
