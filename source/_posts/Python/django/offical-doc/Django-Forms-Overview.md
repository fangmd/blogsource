---
title: Django Forms - Overview
date: 2016-12-13 13:18:12
tags: [Django, Forms]
category: Python

---

Overview

# HTML Forms

在 HTML 中表单由`<form>...</form>`标签生成。

`<input>`元素，需要定义：

1. where:用户的输入该返回什么 URL
2. how:数据应该使用哪种 HTTP method 返回

## GET and POST

GET and POST are the only HTTP methods to use when dealing with forms.

# Django’s role in forms

- preparing and restructuring data to make it ready for rendering
- creating HTML forms for the data
- receiving and processing submitted forms and data from the client

# Forms in Django

We’ve described HTML forms briefly, but an HTML <form> is just one part of the machinery required.

## The Django Form class

## Instantiating, processing, and rendering forms

1. get hold of it in the view (fetch it from the database, for example)
2. pass it to the template context
3. expand it to HTML markup using template variables


# Building a form

## The work that needs to be done

比如需要创建一个下面的 html：

    <form action="/your-name/" method="post">
        <label for="your_name">Your name: </label>
        <input id="your_name" type="text" name="your_name" value="{{ current_name }}">
        <input type="submit" value="OK">
    </form>

## Building a form in Django

### The Form class

forms.py:

    from django import forms

    class NameForm(forms.Form):
        your_name = forms.CharField(label='Your name', max_length=100)

forms class 有一个 is_valid() 方法，用于验证数据的有效性

这个 forms 被渲染后的 html：

    <label for="your_name">Your name: </label>
    <input id="your_name" type="text" name="your_name" maxlength="100" required />

没有包含 `<form>` 标签，和 submit button

### The view

views.py：

    from django.shortcuts import render
    from django.http import HttpResponseRedirect

    from .forms import NameForm

    def get_name(request):
        # if this is a POST request we need to process the form data
        if request.method == 'POST':
            # create a form instance and populate it with data from the request:
            form = NameForm(request.POST)
            # check whether it's valid:
            if form.is_valid():
                # process the data in form.cleaned_data as required
                # ...
                # redirect to a new URL:
                return HttpResponseRedirect('/thanks/')

        # if a GET (or any other method) we'll create a blank form
        else:
            form = NameForm()

        return render(request, 'name.html', {'form': form})


# Form classes

所有的 Form class 都继承自 `django.forms.Form`, 包括 Django 的 admin 中使用的 `ModelForm`

>Form 用于直接添加和编辑 Django model。

## Bound and unbound form instances

Bound 和 unbound 的区别：

1. 一个 unbound 的 form 没有和它连接的数据，当它被渲染显示给用户的时候，它是空的或者包含默认的值。
2. 一个 bound form，渲染后是包含数据的。

form 的 `is_bound` 属性表示了是否绑定数据。

## More on fields

下面创建一个更有意义的 form：

forms.py:

    from django import forms

    class ContactForm(forms.Form):
        subject = forms.CharField(max_length=100)
        message = forms.CharField(widget=forms.Textarea)
        sender = forms.EmailField()
        cc_myself = forms.BooleanField(required=False)

### Widgets

每个 form field 都有一个对应的 Widget class,用于设置其 input 的 type，默认 type="text"

#### Field data

通过调用 `forms.is_valid()` 来验证用户数据,验证后的数据会被保存的到 `form.cleaned_data` dictionary,这里的数据可以很好的转化成 Python 数据

views.py:

    from django.core.mail import send_mail

    if form.is_valid():
        subject = form.cleaned_data['subject']
        message = form.cleaned_data['message']
        sender = form.cleaned_data['sender']
        cc_myself = form.cleaned_data['cc_myself']

        recipients = ['info@example.com']
        if cc_myself:
            recipients.append(sender)

        send_mail(subject, message, sender, recipients)
        return HttpResponseRedirect('/thanks/')

一些 field 类型需要一些额外的处理，比如 使用 form 上传文件

# Working with form templates

把 form 对象放到 template 的 conext 中就可以把 form 添加到 template 中，比如表单的变量名是 form，`{{ form }}` 加入到 template 中就可以了。

## Form rendering options

一些定制：

- `{{ form.as_table }}`: 生成的 html 会被 `<tr>` 标签包裹
- `{{ form.as.p }}`: 被 `<p>` 包裹
- `{{ form.as_ul }}`: 被 `<li>` 包裹

在 template 中需要自己写 `<table>`,`<ul>` 标签

`{{ form.as_p }}` ContactForm 的输出：

    <p><label for="id_subject">Subject:</label>
        <input id="id_subject" type="text" name="subject" maxlength="100" required /></p>
    <p><label for="id_message">Message:</label>
        <textarea name="message" id="id_message" required></textarea></p>
    <p><label for="id_sender">Sender:</label>
        <input type="email" name="sender" id="id_sender" required /></p>
    <p><label for="id_cc_myself">Cc myself:</label>
        <input type="checkbox" name="cc_myself" id="id_cc_myself" /></p>

每个 field 都有一个 ID `id_<field-name>`

## Rendering fields manually

手动渲染 fields，每个 field 都可以通过属性 `{{ form.name_of_field }}` 获取

ex：

    {{ form.non_field_errors }}
    <div class="fieldWrapper">
        {{ form.subject.errors }}
        <label for="{{ form.subject.id_for_label }}">Email subject:</label>
        {{ form.subject }}
    </div>
    <div class="fieldWrapper">
        {{ form.message.errors }}
        <label for="{{ form.message.id_for_label }}">Your message:</label>
        {{ form.message }}
    </div>
    <div class="fieldWrapper">
        {{ form.sender.errors }}
        <label for="{{ form.sender.id_for_label }}">Your email address:</label>
        {{ form.sender }}
    </div>
    <div class="fieldWrapper">
        {{ form.cc_myself.errors }}
        <label for="{{ form.cc_myself.id_for_label }}">CC yourself?</label>
        {{ form.cc_myself }}
    </div>

`<label>` 可以通过 `label_tag()` 方法生成，ex：

    <div class="fieldWrapper">
        {{ form.subject.errors }}
        {{ form.subject.label_tag }}
        {{ form.subject }}
    </div>

### Rendering form error messages

。。。

## Looping over the form’s fields

使用遍历 field 的方式生成 forms：

    {% for field in form %}
        <div class="fieldWrapper">
            {{ field.errors }}
            {{ field.label_tag }} {{ field }}
            {% if field.help_text %}
            <p class="help">{{ field.help_text|safe }}</p>
            {% endif %}
        </div>
    {% endfor %}

`{{ field.label }}`

`{{ field.label_tag }}`

`{{ field.id_for_label }}`

`{{ field.value }}`

`{{ field.html_name }}`

`{{ field.help_text }}`

`{{ field.errors }}`

`{{ field.is_hidden }}`

`{{ field.field }}`

### Looping over hidden and visible fields

    {# Include the hidden fields #}
    {% for hidden in form.hidden_fields %}
    {{ hidden }}
    {% endfor %}
    {# Include the visible fields #}
    {% for field in form.visible_fields %}
        <div class="fieldWrapper">
            {{ field.errors }}
            {{ field.label_tag }} {{ field }}
        </div>
    {% endfor %}

## Reusable form templates

把 form 的 html 写到一个 html 文件文件中，通过 `include` 达到复用的效果

    # In your form template:
    {% include "form_snippet.html" %}

    # In form_snippet.html:
    {% for field in form %}
        <div class="fieldWrapper">
            {{ field.errors }}
            {{ field.label_tag }} {{ field }}
        </div>
    {% endfor %}
