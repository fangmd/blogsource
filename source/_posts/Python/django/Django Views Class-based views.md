---
title: Django Views Class-based views
date: 2016-10-03 13:18:12
tags: [Django, Views]
category: Python

---


# Over view

## Basic examples


## Simple usage in your URLconf

`as_view()`

    from django.conf.urls import url
    from django.views.generic import TemplateView

    urlpatterns = [
        url(r'^about/$', TemplateView.as_view(template_name="about.html")),
    ]

`as_view` 的参数会覆盖类中相应的属性

## Subclassing generic views

    # some_app/views.py
    from django.views.generic import TemplateView

    class AboutView(TemplateView):
        template_name = "about.html"



    # urls.py
    from django.conf.urls import url
    from some_app.views import AboutView

    urlpatterns = [
        url(r'^about/$', AboutView.as_view()),
    ]

## Supporting other HTTP methods

example：get books

URLconf：

    from django.conf.urls import url
    from books.views import BookListView

    urlpatterns = [
        url(r'^books/$', BookListView.as_view()),
    ]

view：

    from django.http import HttpResponse
    from django.views.generic import ListView
    from books.models import Book

    class BookListView(ListView):
        model = Book

        def head(self, *args, **kwargs):
            last_book = self.get_queryset().latest('publication_date')
            response = HttpResponse('')
            # RFC 1123 date format
            response['Last-Modified'] = last_book.publication_date.strftime('%a, %d %b %Y %H:%M:%S GMT')
            return response

# Build-in class-based generic views 内置的一些通用视图类

## Extending generic views

## Generic views of objects

example：

    # models.py
    from django.db import models

    class Publisher(models.Model):
        name = models.CharField(max_length=30)
        address = models.CharField(max_length=50)
        city = models.CharField(max_length=60)
        state_province = models.CharField(max_length=30)
        country = models.CharField(max_length=50)
        website = models.URLField()

        class Meta:
            ordering = ["-name"]

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Author(models.Model):
        salutation = models.CharField(max_length=10)
        name = models.CharField(max_length=200)
        email = models.EmailField()
        headshot = models.ImageField(upload_to='author_headshots')

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Book(models.Model):
        title = models.CharField(max_length=100)
        authors = models.ManyToManyField('Author')
        publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
        publication_date = models.DateField()

views：

    # views.py
    from django.views.generic import ListView
    from books.models import Publisher

    class PublisherList(ListView):
        model = Publisher

hook that view into urls：

    # urls.py
    from django.conf.urls import url
    from books.views import PublisherList

    urlpatterns = [
        url(r'^publishers/$', PublisherList.as_view()),
    ]

可以通过 template_name 定义模版文件，

context 的名字是 object_list , use context in template：

    {% extends "base.html" %}

    {% block content %}
        <h2>Publishers</h2>
        <ul>
            {% for publisher in object_list %}
                <li>{{ publisher.name }}</li>
            {% endfor %}
        </ul>
    {% endblock %}

### Making “friendly” template contexts

定义 context 的名字：`context_object_name = 'context_name'`

ex:

    # views.py
    from django.views.generic import ListView
    from books.models import Publisher

    class PublisherList(ListView):
        model = Publisher
        context_object_name = 'my_favorite_publishers'



### Adding extra context

添加额外的 context，利用 `get_context_data` 方法


ex：

    from django.views.generic import DetailView
    from books.models import Publisher, Book

    class PublisherDetail(DetailView):

        model = Publisher

        def get_context_data(self, **kwargs):
            # Call the base implementation first to get a context
            context = super(PublisherDetail, self).get_context_data(**kwargs)
            # Add in a QuerySet of all the books
            context['book_list'] = Book.objects.all()
            return context

或者：

    def get_context_data(self, **kwargs):  # TODO:这个方法意思??
        """
        增加额外的数据，这里返回一个文章分类，以字典的形式
        :param kwargs:
        :return:
        """
        kwargs['category_list'] = Category.objects.all().order_by('name')
        return super(IndexView, self).get_context_data(**kwargs)

### Viewing subsets of objects

### Dynamic filtering


### Performing extra work

# Form handling with class-based views

Form 生成有三个步骤：

1. 初始化 GET （生成空的或者填充的表单）
2. POST with invalid data （通常是重新显示 form，并且显示错误信息）
3. POST with valid data （处理数据并且重定向网址）

## Basic forms

a simple contact form:

    #form.py

    from django import forms

    class ContactForm(forms.Form):
        name = forms.CharField()
        message = forms.CharField(widget=forms.Textarea)

        def send_email(self):
            # send email using the self.cleaned_data dictionary
            pass

FormView:

    # views.py
    from myapp.forms import ContactForm
    from django.views.generic.edit import FormView

    class ContactView(FormView):
        template_name = 'contact.html'
        form_class = ContactForm
        success_url = '/thanks/'

        def form_valid(self, form):
            # This method is called when valid form data has been POSTed.
            # It should return an HttpResponse.
            form.send_email()
            return super(ContactView, self).form_valid(form)

Notes:

- FormView 继承 `TemplateResponseMixin`, 所以 `template_name` 可以在这里使用
- 默认 `form_valid()` 实现重定向到： `success_url`

## Model forms




# Using mixins with class-based views


# Built-in class-based views API

- Base views
    - View
    - TemplateView
    - RedirectView
- Generic display views
    - DetailView
    - ListView
- Generic editing views
    - FormView
    - CreateView
    - UpdateView
    - DeleteView
- Generic date views
    - ArchiveIndexView
    - YearArchiveView
    - MonthArchiveView
    - WeekArchiveView
    - DayArchiveView
    - TodayArchiveView
    - DateDetailView
- Class-based views mixins
    - Simple mixins
        - ContextMixin
        - TemplateResponseMixin
    - Single object mixins
        - SingleObjectMixin
        - SingleObjectTemplateResponseMixin
    - Multiple object mixins
        - MultipleObjectMixin
        - MultipleObjectTemplateResponseMixin
    - Editing mixins
        - FormMixin
        - ModelFormMixin
        - ProcessFormView
        - DeletionMixin
    - Date-based mixins
        - YearMixin
        - MonthMixin
        - DayMixin
        - WeekMixin
        - DateMixin
        - BaseDateListView
- Class-based generic views - flattened index
    - Simple generic views
        - View
        - TemplateView
        - RedirectView
    - Detail Views
        - DetailView
    - List Views
        - ListView
    - Editing views
        - FormView
        - CreateView
        - UpdateView
        - DeleteView
    - Date-based views
        - ArchiveIndexView
        - YearArchiveView
        - MonthArchiveView
        - WeekArchiveView
        - DayArchiveView
        - TodayArchiveView
        - DateDetailView

## Specification 规范

A class-based view is deployed into a URL pattern using the as_view() classmethod:

    urlpatterns = [
        url(r'^view/$', MyView.as_view(size=42)),
    ]

# Class-based generic views - flattened index
