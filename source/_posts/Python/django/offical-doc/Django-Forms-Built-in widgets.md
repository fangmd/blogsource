---
title: Django Forms - Built-in Widgets
date: 2016-12-14 13:18:12
tags: [Django, Forms]
category: Python

---

A widget is Django’s representation of an HTML input element. The widget handles the rendering of the HTML, and the extraction of data from a GET/POST dictionary that corresponds to the widget.

# Specifying widgets

当我们定义一个 field 在 form 中时，Django 会使用默认的 widget 作为这个 field 在 html 中的显示控件。

如果要使用非默认的 widget：

    from django import forms

    class CommentForm(forms.Form):
        name = forms.CharField()
        url = forms.URLField()
        comment = forms.CharField(widget=forms.Textarea)


# Setting arguments for widgets

很多 widget 都会有一些额外的参数可以设置，可以设置 widget 的时候设置参数：

    from django import forms

    BIRTH_YEAR_CHOICES = ('1980', '1981', '1982')
    FAVORITE_COLORS_CHOICES = (
        ('blue', 'Blue'),
        ('green', 'Green'),
        ('black', 'Black'),
    )

    class SimpleForm(forms.Form):
        birth_year = forms.DateField(widget=forms.SelectDateWidget(years=BIRTH_YEAR_CHOICES))
        favorite_colors = forms.MultipleChoiceField(
            required=False,
            widget=forms.CheckboxSelectMultiple,
            choices=FAVORITE_COLORS_CHOICES,
        )

# Widgets inheriting from the Select widget



# Customizing widget instances

# Base widget classes

# Built-in widgets

## Widgets handling input of text

### TextInput

     <input type="text" ...>

### NumberInput

    <input type="number" ...>

### EmailInput

    <input type="email" ...>

### URLInput

    <input type="url" ...>

### PasswordInput

     <input type='password' ...>

### HiddenInput

    <input type='hidden' ...>

### DateInput

    <input type='text' ...>

`format` `DATE_INPUT_FORMATS`

### DateTimeInput

    <input type='text' ...>

`format` `DATETIME_INPUT_FORMATS`

### TimeInput

    <input type='text' ...>

`format` `TIME_INPUT_FORMATS`

### Textarea

    <textarea>...</textarea>

## Selector and checkbox widgets

### CheckboxInput

    <input type='checkbox' ...>


### Select

    <select><option ...>...</select>

### NullBooleanSelect

### SelectMultiple

    <select multiple='multiple'>...</select>

### RadioSelect

    <ul>
      <li><input type='radio' name='...'></li>
      ...
    </ul>

### CheckboxSelectMultiple

    <ul>
      <li><input type='checkbox' name='...' ></li>
      ...
    </ul>

## File upload widgets

### FileInput

### ClearableFileInput

## Composite widgets

### MultipleHiddenInput

### SplitDateTimeWidget

### SplitHiddenDateTimeWidget

### SelectDateWidget

###
