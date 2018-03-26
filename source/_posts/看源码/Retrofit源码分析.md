---
title: Retrofit 源码分析
date: 2017-10-11 09:38:14
tags: [Source Code]
categories: android

---

>compile 'com.squareup.retrofit2:retrofit:2.3.0'
>okhttp 3.8.0

# 基本使用

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}

Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com/")
    .build();

GitHubService service = retrofit.create(GitHubService.class);

Call<List<Repo>> call = service.listRepos("octocat");

List<Repo> repos = call.execute().body();
```

<!--more-->

# 涉及到设计模式

- 构建者模式
- 代理模式（动态代理 依靠 java.lang.reflect.Proxy 类实现）

# retrofit2.http 包中有各种注解

```
- Body
- GET, POST, DELETE, PUT
- Field, FieldMap,
- ForumUrlEncode
- HEAD
- Header, HeaderMap, Headers
- HTTP
- Multipart
- OPTIONS
- Part, PartMap
- PATCH
- Path, Query, QueryMap, QueryName
- Streaming
- Url
```

# Retrofit.class

## 创建这个对象

使用构建者模式创建

- Retrofit.Builder.class

构造方法：

```java
  Retrofit(okhttp3.Call.Factory callFactory, HttpUrl baseUrl,
      List<Converter.Factory> converterFactories, List<CallAdapter.Factory> adapterFactories,
      @Nullable Executor callbackExecutor, boolean validateEagerly) {
    this.callFactory = callFactory;
    this.baseUrl = baseUrl;
    this.converterFactories = unmodifiableList(converterFactories); // Defensive copy at call site.
    this.adapterFactories = unmodifiableList(adapterFactories); // Defensive copy at call site.
    this.callbackExecutor = callbackExecutor;
    this.validateEagerly = validateEagerly;
  }
```


## 获取 http 请求方法调用类

```java
  public <T> T create(final Class<T> service) {
    Utils.validateServiceInterface(service);
    if (validateEagerly) {
      eagerlyValidateMethods(service);
    }
    return (T) Proxy.newProxyInstance(service.getClassLoader(), new Class<?>[] { service },
        new InvocationHandler() {
          private final Platform platform = Platform.get();

          @Override public Object invoke(Object proxy, Method method, @Nullable Object[] args)
              throws Throwable {
            // If the method is a method from Object then defer to normal invocation.
            if (method.getDeclaringClass() == Object.class) {
              return method.invoke(this, args);
            }
            if (platform.isDefaultMethod(method)) {
              return platform.invokeDefaultMethod(method, service, proxy, args);
            }
            ServiceMethod<Object, Object> serviceMethod =
                (ServiceMethod<Object, Object>) loadServiceMethod(method);
            OkHttpCall<Object> okHttpCall = new OkHttpCall<>(serviceMethod, args);
            return serviceMethod.callAdapter.adapt(okHttpCall);
          }
        });
  }
```

- `Utils.validateServiceInterface(service);`: 传入的必须是接口
- `eagerlyValidateMethods` 方法中解析 Api接口 中的方法， serviceMethodCache 存储 Method-》ServiceMethod 的关联
- Proxy 动态代理

## 重要成员变量

```java
  private final Map<Method, ServiceMethod<?, ?>> serviceMethodCache = new ConcurrentHashMap<>();

  final okhttp3.Call.Factory callFactory;
  final HttpUrl baseUrl;
  final List<Converter.Factory> converterFactories;
  final List<CallAdapter.Factory> adapterFactories;
  final @Nullable Executor callbackExecutor;
  final boolean validateEagerly;
```







# HttpUrl.class

属于 okhttp3 包中的类，封装 url 地址。

# Retrofit.Builder.class

# java.lang.reflect.Proxy

动态创建代理的类

>根据您指定的所有接口生成一个class byte，该class会继承Proxy类，并实现所有你指定的接口（您在参数中传入的接口数组）；然后再利用您指定的classloader将 class byte加载进系统，最后生成这样一个类的对象，并初始化该对象的一些值，如invocationHandler,以即所有的接口对应的Method成员。 初始化之后将对象返回给调用的客户端。这样客户端拿到的就是一个实现你所有的接口的Proxy对象。


([http://blog.csdn.net/rokii/article/details/4046098](http://blog.csdn.net/rokii/article/details/4046098))


# ServiceMethod.class

>Adapts an invocation of an interface method into an HTTP call.

解析 Api接口 中的方法。


# OkHttpCall.class 实现了 interface Call.class

发起请求的实际类

```
- @Override public void enqueue(final Callback<T> callback)
- @Override public Response<T> execute() throws IOException
```


