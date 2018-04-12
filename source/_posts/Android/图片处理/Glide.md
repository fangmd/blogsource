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
    // glide
    implementation ("com.github.bumptech.glide:glide:4.6.1") {
        exclude group: "com.android.support"
    }
    compile 'com.github.bumptech.glide:okhttp3-integration:4.6.1'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.6.1'

    // glide transformations
//    implementation 'jp.wasabeef:glide-transformations:3.1.1'
    // If you want to use the GPU Filters
//    implementation 'jp.co.cyberagent.android.gpuimage:gpuimage-library:1.4.1'
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



## 常用 API

```
thumbnail(float sizeMultiplier). 请求给定系数的缩略图。如果缩略图比全尺寸图先加载完，就显示缩略图，否则就不显示。系数sizeMultiplier必须在(0,1)之间，可以递归调用该方法。
sizeMultiplier(float sizeMultiplier). 在加载资源之前给Target大小设置系数。
**diskCacheStrategy(DiskCacheStrategy strategy).**设置缓存策略。> -DiskCacheStrategy.SOURCE：缓存原始数据，DiskCacheStrategy.RESULT：缓存变换(如缩放、裁剪等)后的资源数据，DiskCacheStrategy.NONE：什么都不缓存，DiskCacheStrategy.ALL：缓存SOURC和RESULT。默认采用> -> -DiskCacheStrategy.RESULT策略，对于download only操作要使用> -DiskCacheStrategy.SOURCE。
priority(Priority priority). 指定加载的优先级，优先级越高越优先加载，但不保证所有图片都按序加载。枚举Priority.IMMEDIATE，Priority.HIGH，Priority.NORMAL，Priority.LOW。默认为Priority.NORMAL。
dontAnimate() . 移除所有的动画。
animate(int animationId). 在异步加载资源完成时会执行该动画。
animate(ViewPropertyAnimation.Animator animator). 在异步加载资源完成时> 会执行该动画。
placeholder(int resourceId). 设置资源加载过程中的占位Drawable。
placeholder(Drawable drawable). 设置资源加载过程中的占位Drawable。
fallback(int resourceId). 设置model为空时要显示的Drawable。如果没设置fallback，model为空时将显示error的Drawable，如果error的Drawable也没设置，就显示placeholder的Drawable。
fallback(Drawable drawable).设置model为空时显示的Drawable。
**error(int resourceId).**设置load失败时显示的Drawable。
**error(Drawable drawable).**设置load失败时显示的Drawable。 -listener(RequestListener《? super ModelType, TranscodeType》> -requestListener). 监听资源加载的请求状态，可以使用两个回调：onResourceReady(R resource, T model, Target target, boolean isFromMemoryCache, boolean isFirstResource)和onException(Exception e, T model, Target<R> target, boolean isFirstResource)，但不要每次请求都使用新的监听器，要避免不必要的内存申请，可以使用单例进行统一的异常监听和处理。
skipMemoryCache(boolean skip). 设置是否跳过内存缓存，但不保证一定不被缓存（比如请求已经在加载资源且没设置跳过内存缓存，这个资源就会被缓存在内存中）。
override(int width, int height). 重新设置Target的宽高值（单位为pixel）。
into(Y target).设置资源将被加载到的Target。
into(ImageView view). 设置资源将被加载到的ImageView。取消该ImageView之前所有的加载并释放资源。
into(int width, int height). 后台线程加载时要加载资源的宽高值（单位为pixel）。
preload(int width, int height). 预加载resource到缓存中（单位为pixel）。
asBitmap(). 无论资源是不是gif动画，都作为Bitmap对待。如果是gif动画会停在第一帧。
**asGif().**把资源作为GifDrawable对待。如果资源不是gif动画将会失败，会回调.error()。
```




# 如何获取图片加载后的时间点

```java
        RequestBuilder requestBuilder = GlideApp.with(context).load(url);
        //添加加载的监听
        requestBuilder.listener(new RequestListener() {
            @Override
            public boolean onLoadFailed(@Nullable GlideException e, Object model, Target target, boolean
                    isFirstResource) {
                listener.loadFail();
                return false;
            }

            @Override
            public boolean onResourceReady(Object resource, Object model, Target target, DataSource dataSource, boolean isFirstResource) {
                listener.loadSuccess();
                return false;
            }
        });
        //缩略图
//        requestBuilder.thumbnail(0.5f);
        requestBuilder.load(url);
        requestBuilder.into(iv);
```


# 裁剪方式 centerCrop, fitCrop

- centerCrop: 缩放图像让图像都测量出来等于或小于 ImageView 的边界范围。该图像将会完全显示，但可能不会填满整个 ImageView
- fitCrop: 缩放图像让它填充到 ImageView 界限内并且裁剪额外的部分。ImageView 会被完全填充，但图像可能不会完整显示

在不设置裁剪类型的时候，裁剪方式由 ImageView 的 scaleType 决定：

- centerCrop: center_crop
- fitCrop: FIT_CENTER，FIT_START，FIT_END, CENTER，CENTER_INSIDE，MATRIX

同时设置 裁剪方式 和 scaleType 的时候，glide 先使用裁剪方式处理图片然后按照 ImageView 的 scaleType 确定图片位置。










# old-----

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

