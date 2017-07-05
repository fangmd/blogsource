---
title: Retrofit
date: 2016-04-11 11:38:14
tags: Retrofit
categories: android

---

# Retrofit 介绍
Retrofit框架是属于Square公司的一个开源框架；

官方简介：A type-safe rest client for Android and java

核心特点：使用接口和注解来描述http请求，减少编写的http请求代码，可读性和可维护性比较好。

核心技术点：1. java动态代理，就是动态生成接口实现类； 2. 反射

让用户创建接口，使用retrofit指定的规则进行网络访问，把接口传入retrofit；
retrofit对接口进行解析，最后进行实际的网络请求。

retrofit所做的事就是简化了用户网络请求的代码

不足：

1. 不支持显示上传和下载请求的进度，不支持端点续传
2. 不可以针对单个请求配置连接和超时时间
3. 不可以在接口中直接配置server和base url
4. 不支持客户端缓存
5. 拦截器不可以对请求进行控制，未附带请求信息，这样直接导致无法实现对请求的签名



# Retrofit 使用

官方：[http://square.github.io/retrofit/](http://square.github.io/retrofit/)

例子请求地址：[http://www.tngou.net/api/lore/classify](http://www.tngou.net/api/lore/classify)

## 0 导包
	compile 'com.squareup.retrofit2:retrofit:2.0.1'

<!--more-->
## 1 创建一个接口

	public interface Service {

	    //@GET("/portal.php")
	    //Call<String> getXML(@Query("mode") String mod);  // 请求到的数据类型,参数：key-value

	    @GET("/api/lore/classify")
	    Call<Tngou> getJson();
	}

- `@Query("..")`请求的参数
- `@GET("...")`，`@POST("...")`请求方式,请求服务器的文件的文件地址
- `@Header({".................."})`头请求

## 2 实体类
>这里没有更改属性名但是加注解是为了防止代码混淆出错

	public class Tngou {

    	@SerializedName("status")
    	private boolean status;
    	@SerializedName("tngou")
    	private List<Item> list;
	..getter/setter
	}

	public class Item {

    	@SerializedName("description")
    	private String description;
    	@SerializedName("id")
    	private int id;
    	@SerializedName("keywords")
    	private String keywords;
    	@SerializedName("name")
    	private String name;
    	@SerializedName("seq")
    	private int seq;
    	@SerializedName("title")
    	private String title;
	...getter/setter
	}

## 3 创建 Retrofit对象

	Retrofit retrofit = new Retrofit.Builder()
		.baseUrl("http://www.tngou.net")
		.addConverterFactory(GsonConverterFactory.create()) // retrofit 的转换器 需要导包
		.build();

retrofit的转换器可以自定义，也可以使用提供的转换器，使用提供的转换器需要另外导入包：

>直接导入这个包可以把它相关联的依赖包都导入(比如：retrofit基本包)

	compile 'com.squareup.retrofit2:converter-gson:2.0.1'

### 3.1 自定义转换器

	.addConverterFactory(new Converter.Factory() {
		@Override
		public Converter<ResponseBody, ?> responseBodyConverter(Type type, Annotation[] annotations, Retrofit retrofit) {
			if (type.equals(String.class)) {
				return new Converter<ResponseBody, String>() {
					@Override
					public String convert(ResponseBody value) throws IOException {
						// 将数据转化需要的内容，string 如果是其他实体类，通过string解析让后返回
						return value.toString();
					}
				};
			}
			return super.responseBodyConverter(type, annotations, retrofit);
		}
	})

## 4 Retrofit对象 连接创建的接口

	Service service = retrofit.create(Service.class);

## 5 执行接口中方法获得一个请求

	mJson = service.getJson();

## 6 开启请求
1. 同步

		Response<String> execute = mCall.execute(); // 同步方法 不能在主线程中执行
2. 异步

		mJson.enqueue(new Callback<Tngou>() {
            /**
             * 在UI线程中
             * @param call
             * @param response
             */
            @Override
            public void onResponse(Call<Tngou> call, Response<Tngou> response) {
				Tngou tngou = response.body(); // body的类型由转换器和泛型决定
            }

            /**
             * 在UI线程中
             * @param call
             * @param t
             */
            @Override
            public void onFailure(Call<Tngou> call, Throwable t) {

            }
        });

## 7 结束

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mJson.cancel();
    }

## 8 xml 解析

解析地址：[http://www.inexus.co/portal.php?mod=rss&catid=0](http://www.inexus.co/portal.php?mod=rss&catid=0)

### 0 实体类

	@Root(name = "item", strict = false)
	public class Item {

	    //Mon, 11 Apr 2016 03:15:00 +0000
	    private SimpleDateFormat sdfRes = new SimpleDateFormat("E, dd MMM yyyy HH:mm:ss Z", Locale.ENGLISH);
	    private SimpleDateFormat sdfTar = new SimpleDateFormat("yyyy-MM-dd", Locale.CHINA);

	    @Element(name = "title")
	    private String title;
	    @Element(name = "link")
	    private String link;
	    @Element(name = "description")
	    private String description;
	    @Element(name = "category")
	    private String category;
	    @Element(name = "author")
	    private String author;
	    @Element(name = "pubDate")
	    private String pubDate;
	。。。getter、setter

		public String getPubDate() {
        	try {
        	    Date parse = sdfRes.parse(pubDate);
        	    return sdfTar.format(parse);
        	} catch (ParseException e) {
        	    e.printStackTrace();
        	}
        	return "";
    	}
	}

.

	@Root(name = "channel", strict = false) // ，严格检查
	public class Channel {

    	@Element(name = "title")
    	@Path("channel")   // 设置结点位置
    	private String title;
    	@ElementList(name = "item", type = Item.class, inline = true)
    	@Path("channel")
    	private List<Item> list;
	。。。getter、setter
	}

### 1 封装一个工具类将接口作为内部接口

	public class NetworkUtil {
	    private static Service service;

	    static {
	        service =  new Retrofit.Builder()
	                .baseUrl("http://www.inexus.co/")
	                .addConverterFactory(SimpleXmlConverterFactory.create())
	                .build()
	                .create(Service.class);
	    }

	    public static Service getService() {
	        return service;
	    }

	    public interface Service {
	        @GET("/portal.php")
	        Call<Channel> getChannel(@Query("mod") String mod, @Query("catid") int catid);
	    }
	}

### 2 使用

	NetworkUtil.getService().getChannel("rss", 0).enqueue(this);

## 如何获得string

	response.body().string()

# Retrofit debug 查看请求和返回结果的 raw

添加依赖：

	compile 'com.squareup.okhttp3:logging-interceptor:3.4.1'

使用：


    HttpLoggingInterceptor httpLoggingInterceptor = new HttpLoggingInterceptor();
    httpLoggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);

    //手动创建一个OkHttpClient并设置超时时间
    OkHttpClient.Builder httpClientBuilder = new OkHttpClient.Builder();
    httpClientBuilder.connectTimeout(DEFAULT_TIMEOUT, TimeUnit.SECONDS);
    httpClientBuilder.addInterceptor(httpLoggingInterceptor);

    mRetrofit = new Retrofit.Builder()
            .client(httpClientBuilder.build())
            .baseUrl(AppConstants.API.BASE_URL)
            .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
            .addConverterFactory(GsonConverterFactory.create())
            .build();

    mDjangoService = mRetrofit.create(DjangoService.class);

# 记一个 500 错误

服务器端提示：

	RuntimeError: You called this URL via POST, but the URL doesn't end in a slash and you have APPEND_SLASH set. Django can't redirect to the slash URL while maintaining POST data. Change your form to point to 10.0.2.2:8000/snippets/ (note the trailing slash), or set APPEND_SLASH=False in your Django settings.
	[10/Nov/2016 03:04:18] "POST /snippets HTTP/1.1" 500 59659

原因：retrofit post 请求必须以 反斜杠 结尾

# 添加 Authorization

基本 Authorization：

	public class RequestInterceptor implements Interceptor {
		 @Override
		 public Response intercept(Chain chain) throws IOException {

			 String credentials = "double" + ":" + "happy0224";

			 final String basic =
					 "Basic " + Base64.encodeToString(credentials.getBytes(), Base64.NO_WRAP);

			 Request request = chain.request().newBuilder()
					 .addHeader("Content-Type", "application/json")
					 .addHeader("Accept", "application/json") //Accept:application/json
					 .addHeader("Authorization", basic)
					 .build();
			 return chain.proceed(request);
		 }
	 }

	private HttpMethod() {

		HttpLoggingInterceptor httpLoggingInterceptor = new HttpLoggingInterceptor();
		httpLoggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);

		//手动创建一个OkHttpClient并设置超时时间
		OkHttpClient.Builder httpClientBuilder = new OkHttpClient.Builder();
		httpClientBuilder.connectTimeout(DEFAULT_TIMEOUT, TimeUnit.SECONDS);
		httpClientBuilder.addInterceptor(new RequestInterceptor());
		httpClientBuilder.addInterceptor(httpLoggingInterceptor);


		mRetrofit = new Retrofit.Builder()
				.client(httpClientBuilder.build())
				.baseUrl(AppConstants.API.BASE_URL)
				.addCallAdapterFactory(RxJava2CallAdapterFactory.create())
				.addConverterFactory(GsonConverterFactory.create())
				.build();

		mDjangoService = mRetrofit.create(DjangoService.class);

	}

# retrofit 文件上传

>服务器使用 from 表单形式接收 post 请求的时候

接口：

```java
    /**
     * 通过 List<MultipartBody.Part> 传入多个part实现多文件上传
     *
     * @param parts 每个part代表一个
     * @return 状态信息
     */
    @Multipart
    @POST("form/upload/")
    Call<ResponseBody> uploadFilesDjango(@Part MultipartBody.Part file2);
```

part 创建方法：
```java
    public static MultipartBody.Part imageToMultipartBody2(List<File> files) {
        RequestBody requestFile = RequestBody.create(MediaType.parse("multipart/form-data"), files.get(0));
        MultipartBody.Part multipartBody = MultipartBody.Part.createFormData("my_image", files.get(0).getName(), requestFile);
        return multipartBody;
    }
```

***注意***这里的 `my_image` 是由服务器对表单的要求决定的


