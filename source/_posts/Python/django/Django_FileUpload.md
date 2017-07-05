---
title: Django FileUpload
date: 2016-10-01 13:18:12
tags: [Django, FileUpload]
category: Python

---

# 基本流程

## 定义一个有文件属性的 Forms

    class UploadFileForm(forms.Form):
        title = forms.CharField(max_length=20)
        file = forms.FileField()

文件上传后通过`request.FILES`获取上传的文件,`request.FILES['file']`

注意：请求方法必须是`post`并且`from`请求中有属性`enctype="multipart/form-data"` 否则 `request.FILES` 会是空的


<!--more-->

views:将 post 的数据绑定到 form 中

    from django.http import HttpResponseRedirect
    from django.shortcuts import render
    from .forms import UploadFileForm

    # Imaginary function to handle an uploaded file.
    from somewhere import handle_uploaded_file

    def upload_file(request):
        if request.method == 'POST':
            form = UploadFileForm(request.POST, request.FILES)
            if form.is_valid():
                handle_uploaded_file(request.FILES['file'])
                return HttpResponseRedirect('/success/url/')
        else:
            form = UploadFileForm()
        return render(request, 'upload.html', {'form': form})

处理接收到的文件：

    def handle_uploaded_file(f):
        with open('some/file/name.txt', 'wb+') as destination:
            for chunk in f.chunks():
                destination.write(chunk)

这里使用`UploadedFile.chunks()`而不是`read()`确保大文件不会占用系统过多内存

## 使用 model 接收和处理上传的文件
如果要保存 file 到 Model 中的 FileField 中，可以使用 ModelForm。

File 文件会保存到 FileField 的参数 `upload_to` 指定的位置。

有两种方式：

1. 使用自定义的Model对象

        from django.http import HttpResponseRedirect
        from django.shortcuts import render
        from .forms import ModelFormWithFileField

        def upload_file(request):
            if request.method == 'POST':
                form = ModelFormWithFileField(request.POST, request.FILES)
                if form.is_valid():
                    # file is saved
                    form.save()
                    return HttpResponseRedirect('/success/url/')
            else:
                form = ModelFormWithFileField()
            return render(request, 'upload.html', {'form': form})

2. 手动构建一个对象

    from django.http import HttpResponseRedirect
    from django.shortcuts import render
    from .forms import UploadFileForm
    from .models import ModelWithFileField

    def upload_file(request):
        if request.method == 'POST':
            form = UploadFileForm(request.POST, request.FILES)
            if form.is_valid():
                instance = ModelWithFileField(file_field=request.FILES['file'])
                instance.save()
                return HttpResponseRedirect('/success/url/')
        else:
            form = UploadFileForm()
        return render(request, 'upload.html', {'form': form})


# 上传多个文件

    # forms.py
    from django import forms

    class FileFieldForm(forms.Form):
        file_field = forms.FileField(widget=forms.ClearableFileInput(attrs={'multiple': True}))



    # views.py
    from django.views.generic.edit import FormView
    from .forms import FileFieldForm

    class FileFieldView(FormView):
        form_class = FileFieldForm
        template_name = 'upload.html'  # Replace with your template.
        success_url = '...'  # Replace with your URL or reverse().

        def post(self, request, *args, **kwargs):
            form_class = self.get_form_class()
            form = self.get_form(form_class)
            files = request.FILES.getlist('file_field')
            if form.is_valid():
                for f in files:
                    ...  # Do something with each file.
                return self.form_valid(form)
            else:
                return self.form_invalid(form)

## Upload Handlers



### 上传的文件在那里存储 Where uploaded data is stored

在文件上传完成之前，文件需要保存在一个地方

默认下：文件小于2.5m会保存在内存中，意味着读写会很快。如果文件比较大，会创建在本地创建一个临时存储位置。

# File 对象

`django.core.files`模块和其子模块包含了一些文件处理的方法。

## File 类

是对file对象的简单封装并且包含了django的一些特性。

有一下属性和方法：

1. name

    包含`MEDIA_ROOT`相对路径的文件名

2. size

    文件的大小

3. file

    这个类所封装的原声file对象
4. mode

    文件的读写模式
5. open(mode=None)

    打开或者重新打开文件，它的参数和Python内部的`open`方法参数一样，如果是重新打开，mode会覆盖上次的mode。
6. read(num_bytes=None)

    从文件中读取内容，参数为从文件中读取的大小，不指定就表示读取全部
7. __iter__()

    迭代整个文件，每次生成一行。
8. chunks(chunk_size=None)

    从文件中读取一块数据，参数指定了数据的大小，默认是64k。
    这个方法可以避免读取大文件时占用过多运行内存。
9. multiple_chunks(chunk_size=None)

    如果文件足够大，需要按照参数指定的大小来切成几个部分来读取，就会返回`True`
10. write(content)

    将内容str写入到文件中。在有些系统中，内容不会写入成功在调用`close`之前
11. close()

    关闭文件

In addition to the listed methods, File exposes the following attributes and methods of its file object: encoding, fileno, flush, isatty, newlines, read, readinto, readlines, seek, softspace, tell, truncate, writelines, xreadlines. If you are using Python 3, the seekable method is also available.

## ContentFile class

继承自`File`class，用来操作字节和字符内容，而不是实际的文件

## ImageFile class
继承自`File`class,转为图像文件设计，包含了下面两个属性：

1. width：pixel，宽像素
2. height：pixel，高像素

## Additional methods on files attached to objects

### File.save(name, content, save=True)

### File.delete(save=True)

# File Storage API

## Getting the current storage class

django提供了两种方法来获取当前的存储类：

1. class DefaultStorage

    提供了对当前默认存储系统的延迟访问
2. get_storage_class(import_path=None)

    返回实现存储API的类或者Model
## FileSystemStorage Class

class FileSystemStorage(location=None, base_url=None, file_permissions_mode=None, directory_permissions_mode=None)

location: 文件的绝对路径，默认是`MEDIA_ROOT`指定的值。

base_url:存储在本地的文件的URL，默认是`MEDIA_URL`

file_permissions_mode:文件的读写权限

directory_permissions_mode：目录的读写权限

## Storate class

提供了文件存储的标准API，所有文件存储类可以按需继承他来实现。

。。。。一些方法介绍

# 管理文件

django使用`MEDIA_URL`,`MEDIA_ROOT`设置本地存储文件。

## 在Model中使用文件

使用`FileField`,`ImageField`

ex:

    from django.db import models

    class Car(models.Model):
    name = models.CharField(max_length=255)
    price = models.DecimalField(max_digits=5, decimal_places=2)
    photo = models.ImageField(upload_to='cars')

该变一个文件的文件名：

    >>> import os
    >>> from django.conf import settings
    >>> initial_path = car.photo.path
    >>> car.photo.name = 'cars/chevy_ii.jpg'
    >>> new_path = settings.MEDIA_ROOT + car.photo.name
    >>> # Move the file on the filesystem
    >>> os.rename(initial_path, new_path)
    >>> car.save()
    >>> car.photo.path
    '/media/cars/chevy_ii.jpg'
    >>> car.photo.path == new_path
    True

## File object

`django.core.files.File`

构建File对象：

    >>> from django.core.files import File

    # Create a Python file object using open()
    >>> f = open('/path/to/hello.world', 'w')
    >>> myfile = File(f)

## 文件存储
