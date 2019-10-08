---
title: Django Forms - Built-in fields
date: 2016-12-14 13:18:12
tags: [Django, Forms]
category: Python

---


# Core field arguments

### class Field(**kwargs)

当创建一个 Form class 的时候，最重要的是定义 form 的 fields。

每个 field 有自己的验证逻辑。

### Filed.clean(value)

每个 Field 实例都有 `clean()` 方法：接收一个数据参数，数据非法就抛出 `django.forms.ValidationError` 异常，数据正常就返回数据：

    >>> from django import forms
    >>> f = forms.EmailField()
    >>> f.clean('foo@example.com')
    'foo@example.com'
    >>> f.clean('invalid email address')
    Traceback (most recent call last):
    ...
    ValidationError: ['Enter a valid email address.']

## Core field arguments

Field 类的构造方法通常需要下面的参数

## required

### Field.required

默认 `required=True`

    >>> from django import forms
    >>> f = forms.CharField()
    >>> f.clean('foo')
    'foo'
    >>> f.clean('')
    Traceback (most recent call last):
    ...
    ValidationError: ['This field is required.']
    >>> f.clean(None)
    Traceback (most recent call last):
    ...
    ValidationError: ['This field is required.']
    >>> f.clean(' ')
    ' '
    >>> f.clean(0)
    '0'
    >>> f.clean(True)
    'True'
    >>> f.clean(False)
    'False'

定义参数可为空：

    >>> f = forms.CharField(required=False)
    >>> f.clean('foo')
    'foo'
    >>> f.clean('')
    ''
    >>> f.clean(None)
    ''
    >>> f.clean(0)
    '0'
    >>> f.clean(True)
    'True'
    >>> f.clean(False)
    'False'

表示 field 可以不填

## label

### Field.label

The label argument lets you specify the “human-friendly” label for this field. This is used when the Field is displayed in a Form.

    >>> from django import forms
    >>> class CommentForm(forms.Form):
    ...     name = forms.CharField(label='Your name')
    ...     url = forms.URLField(label='Your website', required=False)
    ...     comment = forms.CharField()
    >>> f = CommentForm(auto_id=False)
    >>> print(f)
    <tr><th>Your name:</th><td><input type="text" name="name" required /></td></tr>
    <tr><th>Your website:</th><td><input type="url" name="url" required /></td></tr>
    <tr><th>Comment:</th><td><input type="text" name="comment" required /></td></tr>

## label_suffix

### Field.label_suffix

    >>> class ContactForm(forms.Form):
    ...     age = forms.IntegerField()
    ...     nationality = forms.CharField()
    ...     captcha_answer = forms.IntegerField(label='2 + 2', label_suffix=' =')
    >>> f = ContactForm(label_suffix='?')
    >>> print(f.as_p())
    <p><label for="id_age">Age?</label> <input id="id_age" name="age" type="number" required /></p>
    <p><label for="id_nationality">Nationality?</label> <input id="id_nationality" name="nationality" type="text" required /></p>
    <p><label for="id_captcha_answer">2 + 2 =</label> <input id="id_captcha_answer" name="captcha_answer" type="number" required /></p>

## initial

### Field.initial

该参数会被使用，当 Field 是在一个 unbound Form 的时候。

    >>> from django import forms
    >>> class CommentForm(forms.Form):
    ...     name = forms.CharField(initial='Your name')
    ...     url = forms.URLField(initial='http://')
    ...     comment = forms.CharField()
    >>> f = CommentForm(auto_id=False)
    >>> print(f)
    <tr><th>Name:</th><td><input type="text" name="name" value="Your name" required /></td></tr>
    <tr><th>Url:</th><td><input type="url" name="url" value="http://" required /></td></tr>
    <tr><th>Comment:</th><td><input type="text" name="comment" required /></td></tr>

为什么不把 initial 直接作为 data 传入？

>因为作为 data 就会触发数据验证，initial 里的数据通常是非法的可能会报错

可以使用方法作为 initial：

    >>> import datetime
    >>> class DateForm(forms.Form):
    ...     day = forms.DateField(initial=datetime.date.today)
    >>> print(DateForm())
    <tr><th>Day:</th><td><input type="text" name="day" value="12/23/2008" required /><td></tr>

## widget

### Field.widget

## help_text

### Field.help_text

## error_messages


### Field.error_messages

    >>> name = forms.CharField(error_messages={'required': 'Please enter your name'})
    >>> name.clean('')
    Traceback (most recent call last):
      ...
    ValidationError: ['Please enter your name']

## validators

### Field.validators

## localize

### Field.localize

## disabled

### Field.disabled


# Checking if the field data has changed

## has_changed()

### Field.has_changed(）



# Built-in Field classes

## BooleanField

### class BooleanField(**kwargs)

- Default widget: CheckboxInput
- Empty value: False
- Normalizes to: A Python True or False value.
- Validates that the value is True (e.g. the check box is checked) if the field has required=True.
- Error message keys: required

## CharField

- Default widget: TextInput
- Empty value: '' (an empty string)
- Normalizes to: A Unicode object.
- Validates max_length or min_length, if they are provided. Otherwise, all inputs are valid.
- Error message keys: required, max_length, min_length

三个用于验证的参数：

- max_length
- min_length
- strip

## ChoiceField

- Default widget: Select
- Empty value: '' (an empty string)
- Normalizes to: A Unicode object.
- Validates that the given value exists in the list of choices.
- Error message keys: required, invalid_choice

## TypedChoiceField

- Default widget: Select
- Empty value: Whatever you’ve given as empty_value.
- Normalizes to: A value of the type provided by the coerce argument.
- Validates that the given value exists in the list of choices and can be coerced.
- Error message keys: required, invalid_choice

## DateField

## DateTimeField

## DecimalField

## DurationField

## EmailField

## FileField

- Default widget: ClearableFileInput
- Empty value: None
- Normalizes to: An UploadedFile object that wraps the file content and file name into a single object.
- Can validate that non-empty file data has been bound to the form.
- Error message keys: required, invalid, missing, empty, max_length

## FilePathField

## FloatField

## ImageField

## IntegerField

## GenericIPAddressField

## MultipleChoiceField

## TypedMultipleChoiceField

## NullBooleanField

## RegexField

## SlugField

## TimeField

## URLField

## UUIDField


# Slightly complex built-in Field classes

## ComboField

## MultiValueField

## SplitDateTimeField


# Fields which handle relationships

# Creating custom fields
