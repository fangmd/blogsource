---
title: Retrofit 缓存
date: 2016-12-29 11:38:14
tags: [Retrofit, 缓存]
categories: Android

---


# 类型一：有网没网都先读缓存
>(60s内)

拦截器：
```java
    Interceptor mCacheInterceptor = new Interceptor() {
        @Override
        public Response intercept(Chain chain) throws IOException {
            Request request = chain.request();
            Log.d(TAG, "request="+request);
            Response response = chain.proceed(request);
            Log.d(TAG, "response="+response);

            String cacheControl = request.cacheControl().toString();
            if (TextUtils.isEmpty(cacheControl)) {
                cacheControl = "public, max-age=60";
            }
            return response.newBuilder()
                    .header("Cache-Control", cacheControl)
                    .removeHeader("Pragma")
                    .build();
        }
    };
```

- 设置max-age为60s之后，这60s之内不管你有没有网,都读缓存
- max-stale设置为4周，意思是，网络未连接的情况下设置缓存时间为4周


client 设置
```java
File cacheFile = new File(context.getCacheDir(), "[缓存目录]");
Cache cache = new Cache(cacheFile, 1024 * 1024 * 100); //100Mb
OkHttpClient client = new OkHttpClient.Builder()
                ...
                .addNetworkInterceptor(interceptor)
                .cache(cache)
                .build();

retrofit = new Retrofit.Builder()
            .client(client)
            ...
```

***注意：***这里要使用 `addNetworkInterceptor`

# 类型二 无网使用缓存，有网就获取最新数据

```java
    /**
     * 云端响应头拦截器，用来配置缓存策略
     * Dangerous interceptor that rewrites the server's cache-control header.
     */
    private final Interceptor REWRITE_CACHE_CONTROL_INTERCEPTOR = chain -> {
        Request request = chain.request();
        if(!NetUtils.hasNetwork(context)){
            request = request.newBuilder()
                    .cacheControl(CacheControl.FORCE_CACHE)
                    .build();
            Log.d(TAG, "no network");
        }
        Response originalResponse = chain.proceed(request);
        if(NetUtils.hasNetwork(context)){
            //有网的时候读接口上的@Headers里的配置，你可以在这里进行统一的设置
            String cacheControl = request.cacheControl().toString();
            return originalResponse.newBuilder()
                    .header("Cache-Control", cacheControl)
                    .removeHeader("Pragma")
                    .build();
        }else{
            return originalResponse.newBuilder()
                    .header("Cache-Control", "public, only-if-cached, max-stale=2419200")
                    .removeHeader("Pragma")
                    .build();
        }
    };
```


***注意***这个 interceptor 需要使用 `.addInterceptor(mCacheInterceptor)`


# Stack overflow

```
private BackendService() {

    httpCacheDirectory = new File(context.getCacheDir(),  "responses");
    int cacheSize = 10 * 1024 * 1024; // 10 MiB
    Cache cache = new Cache(httpCacheDirectory, cacheSize);

    httpClient = new OkHttpClient.Builder()
            .addNetworkInterceptor(REWRITE_RESPONSE_INTERCEPTOR)
            .addInterceptor(OFFLINE_INTERCEPTOR)
            .cache(cache)
            .build();

    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl("https://api.backend.com")
            .client(httpClient)
            .addConverterFactory(GsonConverterFactory.create())
            .build();

    backendApi = retrofit.create(BackendApi.class);
}

private static final Interceptor REWRITE_RESPONSE_INTERCEPTOR = chain -> {
    Response originalResponse = chain.proceed(chain.request());
    String cacheControl = originalResponse.header("Cache-Control");

    if (cacheControl == null || cacheControl.contains("no-store") || cacheControl.contains("no-cache") ||
            cacheControl.contains("must-revalidate") || cacheControl.contains("max-age=0")) {
        return originalResponse.newBuilder()
                .header("Cache-Control", "public, max-age=" + 10)
                .build();
    } else {
        return originalResponse;
    }
};

private static final Interceptor OFFLINE_INTERCEPTOR = chain -> {
    Request request = chain.request();

    if (!isOnline()) {
        Log.d(TAG, "rewriting request");

        int maxStale = 60 * 60 * 24 * 28; // tolerate 4-weeks stale
        request = request.newBuilder()
                .header("Cache-Control", "public, only-if-cached, max-stale=" + maxStale)
                .build();
    }

    return chain.proceed(request);
};

public static boolean isOnline() {
    ConnectivityManager cm = (ConnectivityManager) MyApplication.getApplication().getSystemService(Context.CONNECTIVITY_SERVICE);
    NetworkInfo netInfo = cm.getActiveNetworkInfo();
    return netInfo != null && netInfo.isConnectedOrConnecting();
}

```

参考：

- [http://www.jianshu.com/p/9c3b4ea108a7](http://www.jianshu.com/p/9c3b4ea108a7)
- [http://blog.csdn.net/picasso_l/article/details/50579884](http://blog.csdn.net/picasso_l/article/details/50579884)