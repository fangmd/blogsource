---
title: OKHttp 源码分析
date: 2017-10-09 09:38:14
tags: [Source Code]
categories: android

---

>compile 'com.squareup.retrofit2:retrofit:2.3.0'
>okhttp 3.8.0

# OkHttpClient.class

作用：

1. 发送 http 请求
2. 读取 http 请求结果

在一个工程中建议自己维护一个单例 OkHttpClient 对象，因为每个 OkHttpClient 对象都有自己的 连接池和线程池，每次请求都创建一个 OkHttpClient 对象会导致性能消耗过大。

## 创建 OkHttpClient 对象

在单例类中创建成员变量 OkHttpClient：

```java
//0
public final OkHttpClient client = new OkHttpClient();

//1 使用 OkHttpClient.Builder 定义一些参数

 public final OkHttpClient client = new OkHttpClient.Builder()
    .addInterceptor(new HttpLoggingInterceptor())
    .cache(new Cache(cacheDir, cacheSize))
    .build();

//2 在代码中动态设置 OkHttpClient 的参数，通过 newBuilder 方法重新设置参数
OkHttpClient eagerClient = client.newBuilder()
    .readTimeout(500, TimeUnit.MILLISECONDS)
    .build();
Response response = eagerClient.newCall(request).execute();
```


<!--more-->


## OkHttpClient 构建者模式 框架简化

```java
public class MyClient {
    String msg;
    String msg1;

    public MyClient(){
        this(new Builder());
    }

    MyClient(Builder builder) {
        this.msg = builder.msg;
        this.msg1 = builder.msg1;
    }

    public Builder newBuilder(){
        return new Builder(this);
    }

    public static final class Builder{

        String msg;
        String msg1;

        public Builder() {
            msg = "default msg";
            msg1 = "default msg1";
        }

        Builder(MyClient myClient){
            msg = myClient.msg;
            msg1 = myClient.msg1;
        }

        public void setMsg(String msg) {
            this.msg = msg;
        }

        public void setMsg1(String msg1) {
            this.msg1 = msg1;
        }

        public MyClient build(){
            return new MyClient(this);
        }
    }
}
```


## 几个重要的成员变量

- `final Dispatcher dispatcher;`: 处理 `Call` 对象，里面有 请求线程池对象


## 使用 OkHttpClient 获取请求类

```java
  /**
   * Prepares the {@code request} to be executed at some point in the future.
   */
  @Override public Call newCall(Request request) {
    return new RealCall(this, request, false /* for web socket */);
  }
```

在发起请求之前需要设置请求的一些参数，Request 类用于携带请求参数。

返回了 Call 对象，调用 Call 的 `Response execute() throws IOException;`, `void enqueue(Callback responseCallback);` 方法开始网络请求

# Request.class

>同样适用构建者模式创建 Request 对象

## 携带的参数

```java
  final HttpUrl url;
  final String method;
  final Headers headers;
  final @Nullable RequestBody body;
  final Object tag;

  private volatile CacheControl cacheControl; // Lazily initialized.
```

- url: 请求的 url
- method： 请求方法
- headers: 请求头
- RequestBody： 请求体
- tag: 表示标示
- cacheControl：

# Headers.class 请求头类

用于封装请求头的信息。

# HttpUrl.class

URL 地址的封装

# interface Call.class 下面分析其实现类 RealCall.class 代码

作用：

1. 请求的封装
2. 发起请求的方法在这里（OkHttpClient 作用是构建 Call）

## 同步发起请求

```java
  @Override public Response execute() throws IOException {
    synchronized (this) {
      if (executed) throw new IllegalStateException("Already Executed");
      executed = true;
    }
    captureCallStackTrace();
    try {
      client.dispatcher().executed(this);
      Response result = getResponseWithInterceptorChain();
      if (result == null) throw new IOException("Canceled");
      return result;
    } finally {
      client.dispatcher().finished(this);
    }
  }
```

## 异步发起请求

```java
  @Override public void enqueue(Callback responseCallback) {
    synchronized (this) {
      if (executed) throw new IllegalStateException("Already Executed");
      executed = true;
    }
    captureCallStackTrace();
    client.dispatcher().enqueue(new AsyncCall(responseCallback));
  }
```

# Dispatcher.class

>Policy on when async requests are executed.

用于管理发起请求

## 重要的成员变量

- `private @Nullable ExecutorService executorService;`: 维护了请求的线程池


# interface ExecutorService.class

这里使用的是 `ThreadPoolExecutor.class` 

表述了异步执行的机制，并且可以让任务在后台执行。 类似一个线程池。

管理所有的请求。

