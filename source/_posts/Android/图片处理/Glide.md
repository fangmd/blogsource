---
title: Glide
date: 2016-05-10 21:38:14
tags: Glide
categories: android

---


# Glide 4.0

参考：![http://www.jianshu.com/p/ab97d6bda8ec](http://www.jianshu.com/p/ab97d6bda8ec)

## gradle 配置

```
compile 'com.github.bumptech.glide:glide:4.0.0-RC1'
annotationProcessor 'com.github.bumptech.glide:compiler:4.0.0-RC1'
compile 'com.github.bumptech.glide:okhttp3-integration:4.0.0-RC0'
compile 'com.android.support:support-v4:26.0.0-alpha1'
```

如果要把图片缓存本地需要设置权限：

```
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```


<!--more-->

## 0 配置 AppGlideModule

```java

@GlideModule
public class MyGlideModule extends AppGlideModule {

    @Override
    public void applyOptions(final Context context, GlideBuilder builder) {
        //获取内存的默认配置
//        MemorySizeCalculator calculator = new MemorySizeCalculator.Builder(context).build();
//        int defaultMemoryCacheSize = calculator.getMemoryCacheSize();
//        int defaultBitmapPoolSize = calculator.getBitmapPoolSize();
//        int customMemoryCacheSize = (int) (1.2 * defaultMemoryCacheSize);
//        int customBitmapPoolSize = (int) (1.2 * defaultBitmapPoolSize);
//        builder.setMemoryCache(new LruResourceCache(customMemoryCacheSize));
//        builder.setBitmapPool(new LruBitmapPool(customBitmapPoolSize));

        //内存缓存相关,默认是24m
        int memoryCacheSizeBytes = 1024 * 1024 * 20; // 20mb
        builder.setMemoryCache(new LruResourceCache(memoryCacheSizeBytes));


        //设置磁盘缓存及其路径
        //
        int MAX_CACHE_SIZE = 100 * 1024 * 1024;
        String CACHE_FILE_NAME = "imgCache";
        builder.setDiskCache(new ExternalCacheDiskCacheFactory(context,CACHE_FILE_NAME,MAX_CACHE_SIZE));
        if (Environment.MEDIA_MOUNTED.equals(Environment.getExternalStorageState())) {
            String downloadDirectoryPath = Environment.getExternalStorageDirectory().getAbsolutePath() + "/" +
                    CACHE_FILE_NAME;
            //路径---->sdcard/imgCache
            builder.setDiskCache(new DiskLruCacheFactory(downloadDirectoryPath, MAX_CACHE_SIZE));
        } else {
            //路径---->/sdcard/Android/data/<application package>/cache/imgCache
            builder.setDiskCache(new ExternalCacheDiskCacheFactory(context, CACHE_FILE_NAME, MAX_CACHE_SIZE));
        }
    }

    /**
     * 把glide默认的网络请求方式换成 okhttp
     */
    @Override
    public void registerComponents(Context context, Registry registry) {
        registry.replace(GlideUrl.class, InputStream.class, new OkHttpUrlLoader.Factory());
    }

    /**
     * false: 不使用清单配置的方式,减少初始化时间
     */
    @Override
    public boolean isManifestParsingEnabled() {
        return false;
    }
}

```

## 1 RequestBuilder

RequestBuilder 提供了影响加载过程本身的选项:

- thumbnail: 缩略图
- listener: 加载监听
- load: 加载类型
- preload: 
- into: 设置图片加载到哪里
- apply: 添加 RequestOptions 对象
- transition: 添加 TransitionOptions 对象 设置变换图片加载出来的时候替换老图片的过渡效果

### 获取 RequestBuilder

```
RequestBuilder<Drawable> requestBuilder = Glide.with(context).load("image url");
```


## RequestOptions

包括：

- centerCrop
- placeholder
- error
- priority
- disCacheStrategy

```java
private static RequestOptions getRequestOptions() {

        RequestOptions options = new RequestOptions();
        //options.format(DecodeFormat.PREFER_ARGB_8888)
        //options.centerCrop()//图片显示类型
        //options.placeholder(loadingRes)//加载中图片
        //options.error(errorRes)//加载错误的图片
        //options.error(new ColorDrawable(Color.RED))//或者是个颜色值
        //options.priority(Priority.HIGH)//设置请求优先级
        //options.diskCacheStrategy(DiskCacheStrategy.ALL);
        //options.diskCacheStrategy(DiskCacheStrategy.RESOURCE)//仅缓存原图片
        //options.diskCacheStrategy(DiskCacheStrategy.ALL)//全部缓存
        //options.diskCacheStrategy(DiskCacheStrategy.AUTOMATIC)缓存缩略过的
        //options.onlyRetrieveFromCache(true)//仅从缓存加载
        //options.skipMemoryCache(true)//禁用缓存,包括内存和磁盘
        //options.diskCacheStrategy(DiskCacheStrategy.NONE)仅跳过磁盘缓存
        //options.override(200,200)加载固定大小的图片
        //options.dontTransform()不做渐入渐出的转换
        //options.transition(new DrawableTransitionOptions().dontTransition())//同上
        //options.circleCrop()设置成圆形头像<这个是V4.0新增的>
        //options.transform(new RoundedCorners(10))设置成圆角头像<这个是V4.0新增的>

        return options;
    }
```


## TransitionOptions







# old-----

# load
    .load(参数)

参数可以是：

1. String，文件路径，uri，url
2. Uri
3. File
4. Integer，资源id，drawable或者mipmap
5. byte[],model
6. T,model,自定义类型

## 加载到本地



## 加载自定义数据源 --- 待续
通过ModelLoader接口实现，用来加载不同尺寸的图片

实现自己的ModelLoader：

    public interface MyDataModel {
        public String buildUrl(int width, int height);
    }
    public class MyUrlLoader extends BaseGlideUrlLoader<MyDataModel> {
        @Override
        protected String getUrl(MyDataModel model, int width, int height) {
            // Construct the url for the correct size here.
            return model.buildUrl(width, height);
        }
    }

使用：

    Glide.with(this)
         .using(new MyUrlLoader(this))
         .load(new MyDataModel() {
              @Override
              public String buidUrl(int width, int height) {
                  if (width >= 600) {
                      return url1;
                  } else {
                      return url2;
                    }
              }
          })
         .into(imageView);

在高分率的设备上加载大图的url1，在低分辨率的设备上加载小图url2。从而实现了根据不同手机上的像素值大小加载不同尺寸的图片的需求。


# 图片变换
>滤镜，形状

使用glide-transformation

#  加载视频的第一帧

    Uri uri = Uri.fromFile(new File(mPresent.getVideoFilePath()));

    BitmapPool bitmapPool = Glide.get(getApplicationContext()).getBitmapPool();
    int microSecond = 6000000;// 6th second as an example
    VideoBitmapDecoder videoBitmapDecoder = new VideoBitmapDecoder(microSecond);
    FileDescriptorBitmapDecoder fileDescriptorBitmapDecoder = new FileDescriptorBitmapDecoder(videoBitmapDecoder, bitmapPool, DecodeFormat.PREFER_ARGB_8888);
    Glide.with(getApplicationContext())
        .load(uri)
        .asBitmap()
        .override(50,50)// Example
        .videoDecoder(fileDescriptorBitmapDecoder)
        .into(yourImageView);

# load gif

# 站位图


## 正在加载占位图

placeHolder

    .placeholder(R.mipmap.ic_launcher) // can also be a drawable

不能将一个网络图片设置成站位图

## 出错占位图

error

    .error(R.mipmap.future_studio_launcher) // will be displayed if the image cannot

如果图片加载失败就会加载出错占位图

# 图片改变动画

crossFade

    .crossFade()

    .crossFade(500)

可以设置参数：动画的执行时间

## 取消动画

    .dontAnimate()


## 设置一个动态图作为占位图

例子：

创建一个 drawable：（其实自需要里面的 rotate ）

    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
        <item>
            <rotate
                android:drawable="@drawable/group_1"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fromDegrees="0"
                android:toDegrees="360" />
        </item>


    </layer-list>

使用：

    // rotate pro
    final ObjectAnimator anim = ObjectAnimator.ofInt(img, "ImageLevel", 0, MAX_LEVEL);
    anim.setDuration(800);
    anim.setRepeatCount(ObjectAnimator.INFINITE);
    anim.start();

    Glide.with(this)
            .load(path)
            .placeholder(R.drawable.rotate_pro)
            .crossFade()
            .listener(new RequestListener<String, GlideDrawable>() {
                @Override
                public boolean onException(Exception e, String model, Target<GlideDrawable> target, boolean isFirstResource) {
                    anim.cancel();
                    Log.d(TAG, "onException: ");
                    return false;
                }

                @Override
                public boolean onResourceReady(GlideDrawable resource, String model, Target<GlideDrawable> target, boolean isFromMemoryCache, boolean isFirstResource) {
                    anim.cancel();
                    Log.d(TAG, "onResourceReady: ");
                    return false;
                }
            })
            .into(img);

还有擦除动画：（改变 drawable 即可）

    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
        <item>
            <clip
                android:clipOrientation="horizontal"
                android:drawable="@drawable/rectangle"
                android:fromDegrees="0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:toDegrees="360">


            </clip>
        </item>

    </layer-list>


**一个问题：**如果使用 9patch 作为 动画 drawable 的 `android:drawable="@drawable/9-patch"` 时 ImageView 设置的 `scralType=fitXY` 不会立即生效，效果很不好。

# 如何获取图片加载后的时间点

    .into(new GlideDrawableImageViewTarget(photoImg) {
        @Override
        protected void setResource(GlideDrawable resource) {
            // this.getView().setImageDrawable(resource); is about to be called
            super.setResource(resource);
            // here you can be sure it's already set
            // 这里代表图片加载完成
        }
    });
