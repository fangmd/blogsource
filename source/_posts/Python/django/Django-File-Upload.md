---
title: 使用 Django 创建一个文件上传的服务器
date: 2016-12-17 13:18:12
tags: [Django, FileUpload]
category: Python

---

django 版本：1.10.1
python 版本：3.5.0

# 创建工程：upload_demo

    django-admin startproject upload_demo

运行工程：

    python3 manager.py runserver

打开网站查看工程是否创建成功：

    http://127.0.0.1:8000/

![django-welcome](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/django-welcome.png)


<!--more-->
# 创建一个 app：upload_app

    python3 manager.py startapp upload_app

在 'upload_demo/setting.py' 中注册 app：

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'upload_app.apps.UploadAppConfig' # +++
    ]

# 建立文件上传的 url

## 创建 View

`upload_app/views.py`:

    def upload_file(request):
        """
        文件接收 view
        :param request: 请求
        :return:
        """
        if request.method == 'POST':
            pass
        else:
            pass
        return HttpResponse("Hello, world. Upload File.")

## 创建 app 自己的 urls.py

创建文件：`upload_app/urls.py`

    from django.conf.urls import url, include
    from django.contrib import admin

    urlpatterns = [
        url(r'^$', views.upload_file)
    ]

## 注册 app 的 urls.py

将 app 的 url 加入到 `upload_demo/urls.py` 中：

    from django.conf.urls import url, include
    from django.contrib import admin

    urlpatterns = [
        url(r'^admin/', admin.site.urls),
        url(r'^upload/', include('upload_app.urls')) # +++
    ]


访问网址：`http://127.0.0.1:8000/upload/`

看到下面的图片，说明 url 建立成功。

![django_file_url](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/django_file_url.png)

# 使用表单提交文件

## 建立一个表单类

`upload_app/forms.py`:

    from django.forms import forms


    class FileUploadForm(forms.Form):
        my_file = forms.FileField()

## 引入模版

建立文件夹 `templates/`

创建一个 html：`templates/upload_temp.html`

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>

    <form action="/upload/" method="post" enctype="multipart/form-data">
        {% csrf_token %}
        {{ form }}
        <input type="submit" value="提交"/>
    </form>


    </body>
    </html>

在 `upload_demo/settings.py` 中声明这个模版：

    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [os.path.join(BASE_DIR, 'templates')],
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]

## 修改 view

`upload_app/views.py`:

    from django.http import HttpResponse
    from django.shortcuts import render
    from upload_app.forms import FileUploadForm

    def upload_file(request):
        """
        文件接收 view
        :param request: 请求
        :return:
        """
        if request.method == 'POST':
            my_form = FileUploadForm(request.POST, request.FILES)
            if my_form.is_valid():
                f = my_form.cleaned_data['my_file']
                handle_uploaded_file(f)
            return HttpResponse('Upload Success')
        else:
            my_form = FileUploadForm()
        return render(request, 'upload_temp.html', {'form': my_form})


    def handle_uploaded_file(f):
        with open(f.name, 'wb+') as destination:
            for chunk in f.chunks():
                destination.write(chunk)

打开网站:`http://127.0.0.1:8000/upload/` 就可以上传文件了，上传的文件会在工程的根目录下。

# 利用 Model 管理上传的文件

## 创建 Model

`upload_app/models.py`:

    from django.db import models

    class FileSimpleModel(models.Model):
        """
        文件接收 Model
        upload_to：表示文件保存位置
        """
        file_field = models.FileField(upload_to="upload/%Y/%m/%d")

### 更新数据库

创建或者修改了 model 都需要更新以下数据库：

    python3 manage.py makemigrations
    python3 manage.py migrate

这里推荐使用 Navicat 查看数据库：

![django_file_db](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/django_file_db.png)

## 修改 views
在 views 中使用 Model `upload_app/views.py`:

    from django.http import HttpResponse
    from django.shortcuts import render
    from upload_app.forms import FileUploadForm
    from upload_app.models import FileSimpleModel

    def upload_file(request):
        """
        文件接收 view
        :param request: 请求
        :return:
        """
        if request.method == 'POST':
            my_form = FileUploadForm(request.POST, request.FILES)
            if my_form.is_valid():
                # f = my_form.cleaned_data['my_file']
                # handle_uploaded_file(f)
                file_model = FileSimpleModel()
                file_model.file_field = my_form.cleaned_data['my_file']
                file_model.save()
            return HttpResponse('Upload Success')
        else:
            my_form = FileUploadForm()
        return render(request, 'upload_temp.html', {'form': my_form})

下面就可以使用了

上传一个文件后数据库变化： file_field 中保存的是文件的相对路径

![django_file_upload_db](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/django_file_upload_db.png)


为了方便手机端的上传，还需要在 view.py 的函数定义前加上 @csrf_exempt
