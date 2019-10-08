---
title: Django Forms - API
date: 2016-12-13 13:18:12
tags: [Django, Forms]
category: Python

---

# Bound and unbound forms

Form 实例可以 bound 数据也不可 unbound

- 如果 bound 数据，form 可以验证数据的正确性，并且渲染结合数据成 HTML。
- 如果 unbound，form 不能验证数据的正确性（因为没有数据），但是同样可以渲染成 html

### class Form

create Form instance:

    f = ContactForm()

bind data to form 通过 dictionary 作为第一个参数:

    data = {'subject': 'hello', 'message': 'hi there', 'sender': 'foo@example.com', 'cc_myself': True}

    f = ContactForm(data)

dictionary: key 就是 field names,

### is_bound

判断 Form 是否已经绑定数据：

    f = ContactForm()
    f.is_bound # False

    f = ContactForm({'subject': 'hello'})
    f.is_bound # True

    f = ContactForm({})
    f.is_bound # True

一个 Form 实例初始化后就不能改变它所绑定的数据了，如果要改变只能重新创建新的 Form 对象使用

# Using forms to validate data

### Form.clean()

### Form.is_valid()

validate data

### Form.errors

    >>> data = {'subject': '',
    ...         'message': 'Hi there',
    ...         'sender': 'invalid email address',
    ...         'cc_myself': True}
    >>> f = ContactForm(data)
    >>> f.is_valid()

    >>> f.errors
    {'sender': ['Enter a valid email address.'], 'subject': ['This field is required.']}

### Form.errors.as_data()

    >>> f.errors.as_data()
    {'sender': [ValidationError(['Enter a valid email address.'])],
    'subject': [ValidationError(['This field is required.'])]}

### Form.errors.as_json(escape_html=False)

    >>> f.errors.as_json()
    {"sender": [{"message": "Enter a valid email address.", "code": "invalid"}],
    "subject": [{"message": "This field is required.", "code": "required"}]}

### Form.add_error(field, error)

### Form.non_field_errors()

## Behavior of unbound forms

    >>> f = ContactForm()
    >>> f.is_valid()
    False
    >>> f.errors
    {}

# Dynamic initial values

### Form.initial

    >>> f = ContactForm(initial={'subject': 'Hi there!'})



# Checking which form data has changed

### Form.has_changed()

判断是否有数据改变了

    >>> data = {'subject': 'hello',
    ...         'message': 'Hi there',
    ...         'sender': 'foo@example.com',
    ...         'cc_myself': True}
    >>> f = ContactForm(data, initial=data)
    >>> f.has_changed()
    False

当 form 提交的时候，我们创建一个新的 form 实例，并且验证数据比较和原始数据的差别。

    >>> f = ContactForm(request.POST, initial=data)
    >>> f.has_changed()


### Form.changed_data


# Accessing the fields from the form

### Form.fields

    >>> for row in f.fields.values(): print(row)
    ...
    <django.forms.fields.CharField object at 0x7ffaac632510>
    <django.forms.fields.URLField object at 0x7ffaac632f90>
    <django.forms.fields.CharField object at 0x7ffaac3aa050>
    >>> f.fields['name']
    <django.forms.fields.CharField object at 0x7ffaac6324d0>

可以改变 Form 实例的 field：

    >>> f.as_table().split('\n')[0]
    '<tr><th>Name:</th><td><input name="name" type="text" value="instance" required /></td></tr>'
    >>> f.fields['name'].label = "Username"
    >>> f.as_table().split('\n')[0]
    '<tr><th>Username:</th><td><input name="name" type="text" value="instance" required /></td></tr>'

# Accessing "clean" data

### Form.cleaned_data

    >>> data = {'subject': 'hello',
    ...         'message': 'Hi there',
    ...         'sender': 'foo@example.com',
    ...         'cc_myself': True}
    >>> f = ContactForm(data)
    >>> f.is_valid()
    True
    >>> f.cleaned_data
    {'cc_myself': True, 'message': 'Hi there', 'sender': 'foo@example.com', 'subject': 'hello'}

# Outputting forms as HTML

    >>> f = ContactForm()
    >>> print(f)

## as_p()

## as_ul()

## as_table()

## Styling required or erroneous form rows

## Configuring form elements'HTML id attributes and <label> tags

## Notes on field ordering

## How errors are displayed

## Customizing the error list format

# More granular output

The `as_p()`, `as_ul()`, and `as_table()` methods are simply shortcuts – they’re not the only way a form object can be displayed.


### class BoundField

## Attributes of BoundField

## Methods of BoundField

### BoundField.as_hidden(attrs=None, **kwargs)

### BoundField.as_widget(widget=None, attrs=None, only_initial=False)

### BoundField.css_classes()

### BoundField.label_tag(contents=None, attrs=None, label_suffix=None)

### BoundField.value()

# Customizing BoundField

# Binding uploaded files to a form

first，为了能够上传文件，需要在 `<form>` 元素中定义 `enctype="multipart/form-data"`:

    <form enctype="multipart/form-data" method="post" action="/foo/">

secondly，当我们使用 form 的时候，需要绑定文件数据，文件数据的处理和普通的表单数据处理是分开的：

    # Bound form with an image field
    >>> from django.core.files.uploadedfile import SimpleUploadedFile
    >>> data = {'subject': 'hello',
    ...         'message': 'Hi there',
    ...         'sender': 'foo@example.com',
    ...         'cc_myself': True}
    >>> file_data = {'mugshot': SimpleUploadedFile('face.jpg', <file data>)}
    >>> f = ContactFormWithMugshot(data, file_data)


从一个请求中绑定数据：

    # Bound form with an image field, data from the request
    >>> f = ContactFormWithMugshot(request.POST, request.FILES)

unbound：

    # Unbound form with an image field
    >>> f = ContactFormWithMugshot()

## Testing for multipart forms

### Form.is_multipart()

判断表单是否是包含文件上传：

    >>> f = ContactFormWithMugshot()
    >>> f.is_multipart()
    True

template example：

    {% if form.is_multipart %}
        <form enctype="multipart/form-data" method="post" action="/foo/">
    {% else %}
        <form method="post" action="/foo/">
    {% endif %}
    {{ form }}
    </form>


# Subclassing forms    

# Prefixes for forms
