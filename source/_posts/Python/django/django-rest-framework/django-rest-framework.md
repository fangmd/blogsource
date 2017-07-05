---
title: django-rest-framework 官方文档记录
date: 2016-11-07 13:18:12
tags: [Django, django-rest-framework]
category: Python

---


# Tutorial 1: Serialization 序列化

## 安装基本环境

    pip install django
    pip install djangorestframework
    pip install pygments  # We'll be using this for the code highlighting

## 开始 创建测试环境

创建一个 django 工程

    django-admin.py startproject tutorial

在工程中创建一个 app

    python manage.py startapp snippets

<!--more-->

在 `tutorial/settings.py` 文件中修改：声明 app （自己创建的 app 和 rest_framework）

    INSTALLED_APPS = (
        ...
        'rest_framework',
        'snippets.apps.SnippetsConfig',
    )

## 创建 Model 模块

修改 `snippets/models.py` 文件：

    from django.db import models
    from pygments.lexers import get_all_lexers
    from pygments.styles import get_all_styles

    LEXERS = [item for item in get_all_lexers() if item[1]]
    LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
    STYLE_CHOICES = sorted((item, item) for item in get_all_styles())


    class Snippet(models.Model):
        created = models.DateTimeField(auto_now_add=True)
        title = models.CharField(max_length=100, blank=True, default='')
        code = models.TextField()
        linenos = models.BooleanField(default=False)
        language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
        style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

        class Meta:
            ordering = ('created',)

将创建的 Model 建立相应的 db 表，更新数据库：

    python manage.py makemigrations snippets
    python manage.py migrate

## Creating a Serializer class 创建序列化器类

`snippets` app 目录下创建 `serializers.py`:

    from rest_framework import serializers
    from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES


    class SnippetSerializer(serializers.Serializer):
        id = serializers.IntegerField(read_only=True)
        title = serializers.CharField(required=False, allow_blank=True, max_length=100)
        code = serializers.CharField(style={'base_template': 'textarea.html'})
        linenos = serializers.BooleanField(required=False)
        language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
        style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

        def create(self, validated_data):
            """
            Create and return a new `Snippet` instance, given the validated data.
            """
            return Snippet.objects.create(**validated_data)

        def update(self, instance, validated_data):
            """
            Update and return an existing `Snippet` instance, given the validated data.
            """
            instance.title = validated_data.get('title', instance.title)
            instance.code = validated_data.get('code', instance.code)
            instance.linenos = validated_data.get('linenos', instance.linenos)
            instance.language = validated_data.get('language', instance.language)
            instance.style = validated_data.get('style', instance.style)
            instance.save()
            return instance

## Working with Serializers 使用 序列化器类

开启 shell
    python manage.py shell

创建 model 对象，保存到数据库中：

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from rest_framework.renderers import JSONRenderer
    from rest_framework.parsers import JSONParser

    snippet = Snippet(code='foo = "bar"\n')
    snippet.save()

    snippet = Snippet(code='print "hello, world"\n')
    snippet.save()

序列化对象：将 model 对象转化为 python 基本数据类型

    serializer = SnippetSerializer(snippet)
    serializer.data
    # {'title': '', 'id': 3, 'code': 'create by new\n', 'style': 'friendly', 'language': 'python', 'linenos': False}

将 python 基本数据，渲染成 json

    content = JSONRenderer().render(serializer.data)
    content
    # b'{"id":3,"title":"","code":"create by new\\n","linenos":false,"language":"python","style":"friendly"}'

反序列化 json-》object

    from django.utils.six import BytesIO

    stream = BytesIO(content)
    data = JSONParser().parse(stream)

将一个反序列化出来的 object 存到数据库中：

    serializer = SnippetSerializer(data=data) # data 是 json->object 转化出来的 object
    serializer.is_valid()
    # True
    serializer.validated_data
    # OrderedDict([('title', ''), ('code', 'print "hello, world"\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])
    serializer.save()
    # <Snippet: Snippet object>

查询一组数据：

    serializer = SnippetSerializer(Snippet.objects.all(), many=True)
    serializer.data
    # [OrderedDict([('id', 1), ('title', u''), ('code', u'foo = "bar"\n'), ('linenos', False), ('langua......

## Using ModelSerializers 使用 模型序列化

将原来的 `snippets/serializers.py` 文件内容替换为：

    from rest_framework import serializers
    from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES

    class SnippetSerializer(serializers.ModelSerializer):
        class Meta:
            model = Snippet
            fields = ('id', 'title', 'code', 'linenos', 'language', 'style')

使用这个的一个好处是，可以直接看到 serializer 实例的 属性 by printing its representation：

    python manage.py shell


    from snippets.serializers import SnippetSerializer
    serializer = SnippetSerializer()
    print(repr(serializer))
    # SnippetSerializer():
    #    id = IntegerField(label='ID', read_only=True)
    #    title = CharField(allow_blank=True, max_length=100, required=False)
    #    code = CharField(style={'base_template': 'textarea.html'})
    #    linenos = BooleanField(required=False)
    #    language = ChoiceField(choices=[('Clipper', 'FoxPro'), ('Cucumber', 'Gherkin'), ('RobotFramework', 'RobotFramework'), ('abap', 'ABAP'), ('ada', 'Ada')...
    #    style = ChoiceField(choices=[('autumn', 'autumn'), ('borland', 'borland'), ('bw', 'bw'), ('colorful', 'colorful')...

需要注意的是 ModelSerializer 类没有做什么特殊的处理，他只是一个 Serializer 类的简化：

1. An automatically determined set of fields.
2. Simple default implementations for the create() and update() methods.

## Writing regular Django views using our Serializer 使用我们定义的 可序列化对象编写正常的 django View

下面的例子不使用 rest-framework 的特性，按照 django 的方法来建立 view

创建一个 HttpResponse 的子类，用来将 data-》 json

`snippets/views.py`:

    from django.http import HttpResponse
    from django.views.decorators.csrf import csrf_exempt
    from rest_framework.renderers import JSONRenderer
    from rest_framework.parsers import JSONParser
    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer

    class JSONResponse(HttpResponse):
        """
        An HttpResponse that renders its content into JSON.
        """
        def __init__(self, data, **kwargs):
            content = JSONRenderer().render(data)
            kwargs['content_type'] = 'application/json'
            super(JSONResponse, self).__init__(content, **kwargs)

api: 获取已存在的 snippets ，或者创建新的 snippets

    @csrf_exempt
    def snippet_list(request):
        """
        List all code snippets, or create a new snippet.
        """
        if request.method == 'GET':
            snippets = Snippet.objects.all()
            serializer = SnippetSerializer(snippets, many=True)
            return JSONResponse(serializer.data)

        elif request.method == 'POST':
            data = JSONParser().parse(request)
            serializer = SnippetSerializer(data=data)
            if serializer.is_valid():
                serializer.save()
                return JSONResponse(serializer.data, status=201)
            return JSONResponse(serializer.errors, status=400)

`csrf_exempt` 表示 POST 不需要 CSRF token，通常我们不会这样做，rest-framework 中对于这个有更好的解决方案，这里只是一个简单的例子。

api：获取一个独立的 snippets，用于 retrieve, update or delete

    @csrf_exempt
    def snippet_detail(request, pk):
        """
        Retrieve, update or delete a code snippet.
        """
        try:
            snippet = Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            return HttpResponse(status=404)

        if request.method == 'GET':
            serializer = SnippetSerializer(snippet)
            return JSONResponse(serializer.data)

        elif request.method == 'PUT':
            data = JSONParser().parse(request)
            serializer = SnippetSerializer(snippet, data=data)
            if serializer.is_valid():
                serializer.save()
                return JSONResponse(serializer.data)
            return JSONResponse(serializer.errors, status=400)

        elif request.method == 'DELETE':
            snippet.delete()
            return HttpResponse(status=204)

声明上面的 view `snippets/urls.py`： ( wire these views up)

    from django.conf.urls import url
    from snippets import views

    urlpatterns = [
        url(r'^snippets/$', views.snippet_list),
        url(r'^snippets/(?P<pk>[0-9]+)/$', views.snippet_detail),
    ]

声明 url (wire up the root urlconf) `tutorial/urls.py`:

    from django.conf.urls import url, include

    urlpatterns = [
        url(r'^', include('snippets.urls')),
    ]

# Tutorial 2: Requests and Responses 请求和回复

## Request objects

rest-framework 采用 Request 对象，它继承自 HttpRequest，提供了更加灵活的请求解析

其核心是属性： request.data

    request.POST  # Only handles form data.  Only works for 'POST' method.
    request.data # Handles arbitrary data.  Works for 'POST', 'PUT' and 'PATCH' methods.


## Response objects

    return Response(data)  # Renders to content type as requested by the client.

## Status codes

`status` module 提供了请求code, 比如：HTTP_400_BAD_REQUEST

这样比直接使用 数字code 要更加易懂

## Wrapping API views 包装 API 视图

两种包装方式：

1. `@api_view` 修饰方法视图
2. `APIView` 包装以类为视图的api

包装提供了一些新的特性，比如

1. 确认视图接收了 Request 实例， 修改 Response 。
2. 返回 405 Method Not Allowed
3. 捕获 ParseError

## Pulling it all together 使用上面介绍的 rest-framework 的特性编写程序

修改 `snippets/views.py`: 1. 去除 JSONResponse 2. 修改 view

    from rest_framework import status
    from rest_framework.decorators import api_view
    from rest_framework.response import Response
    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer


    @api_view(['GET', 'POST'])
    def snippet_list(request):
        """
        List all snippets, or create a new snippet.
        """
        if request.method == 'GET':
            snippets = Snippet.objects.all()
            serializer = SnippetSerializer(snippets, many=True)
            return Response(serializer.data)

        elif request.method == 'POST':
            serializer = SnippetSerializer(data=request.data)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data, status=status.HTTP_201_CREATED)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    @api_view(['GET', 'PUT', 'DELETE'])
    def snippet_detail(request, pk):
        """
        Retrieve, update or delete a snippet instance.
        """
        try:
            snippet = Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            return Response(status=status.HTTP_404_NOT_FOUND)

        if request.method == 'GET':
            serializer = SnippetSerializer(snippet)
            return Response(serializer.data)

        elif request.method == 'PUT':
            serializer = SnippetSerializer(snippet, data=request.data)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

        elif request.method == 'DELETE':
            snippet.delete()
            return Response(status=status.HTTP_204_NO_CONTENT)

优点：

1. 代码更加简洁
2. 使用 HTTP_400_BAD_REQUEST 而不是 400

## Adding optional format suffixes to our URLs 给 url 添加后缀

修改 view：添加 format 参数

    def snippet_list(request, format=None):
    def snippet_detail(request, pk, format=None):

修改 `urls.py`

    urlpatterns = format_suffix_patterns(urlpatterns)

发起请求回去不同的数据格式：

1. 设置请求头

        Accept:application/json  # Request JSON
        Accept:text/html         # Request HTML
2. 使用 url 后缀

        http http://127.0.0.1:8000/snippets.json  # JSON suffix
        http http://127.0.0.1:8000/snippets.api   # Browsable API suffix

# Tutorial 3: Class-based Views 类视图

## Rewriting our API using class-based views 重写 view

`views.py`:

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from django.http import Http404
    from rest_framework.views import APIView
    from rest_framework.response import Response
    from rest_framework import status


    class SnippetList(APIView):
        """
        List all snippets, or create a new snippet.
        """
        def get(self, request, format=None):
            snippets = Snippet.objects.all()
            serializer = SnippetSerializer(snippets, many=True)
            return Response(serializer.data)

        def post(self, request, format=None):
            serializer = SnippetSerializer(data=request.data)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data, status=status.HTTP_201_CREATED)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


    class SnippetDetail(APIView):
        """
        Retrieve, update or delete a snippet instance.
        """
        def get_object(self, pk):
            try:
                return Snippet.objects.get(pk=pk)
            except Snippet.DoesNotExist:
                raise Http404

        def get(self, request, pk, format=None):
            snippet = self.get_object(pk)
            serializer = SnippetSerializer(snippet)
            return Response(serializer.data)

        def put(self, request, pk, format=None):
            snippet = self.get_object(pk)
            serializer = SnippetSerializer(snippet, data=request.data)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

        def delete(self, request, pk, format=None):
            snippet = self.get_object(pk)
            snippet.delete()
            return Response(status=status.HTTP_204_NO_CONTENT)


修改 `urls.py` :

    from django.conf.urls import url
    from rest_framework.urlpatterns import format_suffix_patterns
    from snippets import views

    urlpatterns = [
        url(r'^snippets/$', views.SnippetList.as_view()),
        url(r'^snippets/(?P<pk>[0-9]+)/$', views.SnippetDetail.as_view()),
    ]

    urlpatterns = format_suffix_patterns(urlpatterns)

## Using mixins

使用类视图好处：

1. 可复用
2. create/retrieve/update/delete 操作在 rest-framework 中都有相应的封装类

修改 `views.py`:

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from rest_framework import mixins
    from rest_framework import generics

    class SnippetList(mixins.ListModelMixin,
                      mixins.CreateModelMixin,
                      generics.GenericAPIView):
        queryset = Snippet.objects.all()
        serializer_class = SnippetSerializer

        def get(self, request, *args, **kwargs):
            return self.list(request, *args, **kwargs)

        def post(self, request, *args, **kwargs):
            return self.create(request, *args, **kwargs)

    class SnippetDetail(mixins.RetrieveModelMixin,
                        mixins.UpdateModelMixin,
                        mixins.DestroyModelMixin,
                        generics.GenericAPIView):
        queryset = Snippet.objects.all()
        serializer_class = SnippetSerializer

        def get(self, request, *args, **kwargs):
            return self.retrieve(request, *args, **kwargs)

        def put(self, request, *args, **kwargs):
            return self.update(request, *args, **kwargs)

        def delete(self, request, *args, **kwargs):
            return self.destroy(request, *args, **kwargs)

添加了基本类：GenericAPIView

mixins 方法扩展类： ListModelMixin，CreateModelMixin，UpdateModelMixin，DestroyModelMixin，RetrieveModelMixin


## Using generic class-based views  使用 generic 基本类

使用 mixin 类能够简化很多代码，但是可以做到更加简洁，使用包含了 mixin 功能的 generic 类

修改 `views.py`:

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from rest_framework import generics


    class SnippetList(generics.ListCreateAPIView):
        queryset = Snippet.objects.all()
        serializer_class = SnippetSerializer


    class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
        queryset = Snippet.objects.all()
        serializer_class = SnippetSerializer

# Tutorial 4: Authentication & Permissions 认证和权限

目的：

1. Code snippets are always associated with a creator.
2. Only authenticated users may create snippets.
3. Only the creator of a snippet may update or delete it.
4. Unauthenticated requests should have full read-only access.

## Adding information to our model 修改 model

修改 `Snippet/models.py`： 添加两个属性

    owner = models.ForeignKey('auth.User', related_name='snippets', on_delete=models.CASCADE)
    highlighted = models.TextField()

添加 保存方法：

    from pygments.lexers import get_lexer_by_name
    from pygments.formatters.html import HtmlFormatter
    from pygments import highlight

    def save(self, *args, **kwargs):
        """
        Use the `pygments` library to create a highlighted HTML
        representation of the code snippet.
        """
        lexer = get_lexer_by_name(self.language)
        linenos = self.linenos and 'table' or False
        options = self.title and {'title': self.title} or {}
        formatter = HtmlFormatter(style=self.style, linenos=linenos,
                                  full=True, **options)
        self.highlighted = highlight(self.code, lexer, formatter)
        super(Snippet, self).save(*args, **kwargs)


修改完数据后更新数据库：这里更新是直接删除旧的数据库，创建新的数据库

    rm -f tmp.db db.sqlite3
    rm -r snippets/migrations
    python manage.py makemigrations snippets
    python manage.py migrate

创建用户：

    python manage.py createsuperuser

## Adding endpoints for our User models 添加用户控制入口

`serializers.py`: 添加 User 序列化器

    from django.contrib.auth.models import User

    class UserSerializer(serializers.ModelSerializer):
        snippets = serializers.PrimaryKeyRelatedField(many=True, queryset=Snippet.objects.all())

        class Meta:
            model = User
            fields = ('id', 'username', 'snippets')

添加 User 对应的 View，修改 `views.py`:

    from django.contrib.auth.models import User
    from snippets.serializers import UserSerializer


    class UserList(generics.ListAPIView):
        queryset = User.objects.all()
        serializer_class = UserSerializer


    class UserDetail(generics.RetrieveAPIView):
        queryset = User.objects.all()
        serializer_class = UserSerializer

添加 url `urls.py`：

    url(r'^users/$', views.UserList.as_view()),
    url(r'^users/(?P<pk>[0-9]+)/$', views.UserDetail.as_view()),


## Associating Snippets with Users 关联 Snippets 和 users

修改 `views.py` 中的 `SnippetList` 类：重写下面的方法，让

    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)

## Updating our serializer 更新 序列化器

上面的代码将 Snippets 和创建它的 user 关联在一起，下面修改 序列化器：添加 field

    owner = serializers.ReadOnlyField(source='owner.username')

## Adding required permissions to views 添加请求权限到 views

rest-framework 中有很多权限类用于限制哪些用户可以请求views，下面我们只用 IsAuthenticatedOrReadOnly 来设置权限

在 `views.py` 中添加 SnippetList ，SnippetDetail 类属性

    from rest_framework import permissions

    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)

## Adding login to the Browsable API 添加游览器登入api

修改 `urls.py` 添加 登入视图url：

    urlpatterns += [
        url(r'^api-auth/', include('rest_framework.urls',
                                   namespace='rest_framework')),
    ]

## Object level permissions 对象级别的权限

下面设置权限： 创建 Snippets 的用户才能修改这个 Snippets

创建 `snippets/permissions.py`: 添加自定义的权限类

    from rest_framework import permissions


    class IsOwnerOrReadOnly(permissions.BasePermission):
        """
        Custom permission to only allow owners of an object to edit it.
        """

        def has_object_permission(self, request, view, obj):
            # Read permissions are allowed to any request,
            # so we'll always allow GET, HEAD or OPTIONS requests.
            if request.method in permissions.SAFE_METHODS:
                return True

            # Write permissions are only allowed to the owner of the snippet.
            return obj.owner == request.user

将权限添加到 SnippetDetail 中 `views.py`:

    from snippets.permissions import IsOwnerOrReadOnly

    permission_classes = (permissions.IsAuthenticatedOrReadOnly,
                          IsOwnerOrReadOnly,)

Now, if you open a browser again, you find that the 'DELETE' and 'PUT' actions only appear on a snippet instance endpoint if you're logged in as the same user that created the code snippet.

## Authenticating with the API 认证api

目前位置我们还没有设置任何 authentication classes ，和权限有关的类，所以目前这个工程的默认认证类是：SessionAuthentication，BasicAuthentication

可以在请求时设置 Basic Auth： 用户名：密码 访问api

# Tutorial 5: Relationships & Hyperlinked APIs 关系和超链接 APIs

## Creating an endpoint for the root of our API 为接口创建一个入口

添加下面代码：`snippets/views.py`

    from rest_framework.decorators import api_view
    from rest_framework.response import Response
    from rest_framework.reverse import reverse


    @api_view(['GET'])
    def api_root(request, format=None):
        return Response({
            'users': reverse('user-list', request=request, format=format),
            'snippets': reverse('snippet-list', request=request, format=format)
        })

上面代码

1. 使用了 rest-framework 提供的 `reverse` 方法来转化url
2. 其对应的 URL 模版，我们会在后面的代码中看到 `snippets/urls.py`

## Creating an endpoint for the highlighted snippets


添加 `snippets/views.py`:

    from rest_framework import renderers
    from rest_framework.response import Response

    class SnippetHighlight(generics.GenericAPIView):
        queryset = Snippet.objects.all()
        renderer_classes = (renderers.StaticHTMLRenderer,)

        def get(self, request, *args, **kwargs):
            snippet = self.get_object()
            return Response(snippet.highlighted)

添加 `snippets/urls.py`:

    url(r'^$', views.api_root),
    url(r'^snippets/(?P<pk>[0-9]+)/highlight/$', views.SnippetHighlight.as_view()),

## Hyperlinking our API 为 api 创建超级链接

处理 实体类 之间的关系有很多种方式：

1. 使用主键
2. Using hyperlinking between entities. 使用超级链接
3. Using a unique identifying slug field on the related entity.
4. Using the default string representation of the related entity.
5. Nesting the related entity inside the parent representation.
6. Some other custom representation.

下面使用超级链接的方式来处理。

修改 序列化器（serializers） 继承 HyperlinkedModelSerializer

HyperlinkedModelSerializer 和 ModelSerializer 的区别：

1. It does not include the id field by default. 默认不包含 id
2. It includes a url field, using HyperlinkedIdentityField。 包含了 url 属性
3. Relationships use HyperlinkedRelatedField, instead of PrimaryKeyRelatedField。 关系维护使用 HyperlinkedRelatedField

修改 `snippets/serializers.py`:

    class SnippetSerializer(serializers.HyperlinkedModelSerializer):
        owner = serializers.ReadOnlyField(source='owner.username')
        highlight = serializers.HyperlinkedIdentityField(view_name='snippet-highlight', format='html')

        class Meta:
            model = Snippet
            fields = ('url', 'id', 'highlight', 'owner',
                      'title', 'code', 'linenos', 'language', 'style')


    class UserSerializer(serializers.HyperlinkedModelSerializer):
        snippets = serializers.HyperlinkedRelatedField(many=True, view_name='snippet-detail', read_only=True)

        class Meta:
            model = User
            fields = ('url', 'id', 'username', 'snippets')

## Making sure our URL patterns are named 确保 url 中定义了参数 name

`snippets/urls.py`:

    from django.conf.urls import url, include
    from rest_framework.urlpatterns import format_suffix_patterns
    from snippets import views

    # API endpoints
    urlpatterns = format_suffix_patterns([
        url(r'^$', views.api_root),
        url(r'^snippets/$',
            views.SnippetList.as_view(),
            name='snippet-list'),
        url(r'^snippets/(?P<pk>[0-9]+)/$',
            views.SnippetDetail.as_view(),
            name='snippet-detail'),
        url(r'^snippets/(?P<pk>[0-9]+)/highlight/$',
            views.SnippetHighlight.as_view(),
            name='snippet-highlight'),
        url(r'^users/$',
            views.UserList.as_view(),
            name='user-list'),
        url(r'^users/(?P<pk>[0-9]+)/$',
            views.UserDetail.as_view(),
            name='user-detail')
    ])

    # Login and logout views for the browsable API
    urlpatterns += [
        url(r'^api-auth/', include('rest_framework.urls',
                                   namespace='rest_framework')),
    ]

## Adding pagination 添加分页

`tutorial/settings.py` :

    REST_FRAMEWORK = {
        'PAGE_SIZE': 10
    }

# Tutorial 6: ViewSets & Routers
