---
title: Glide
date: 2016-05-10 21:38:14
tags: Glide
categories: android

---




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

<!--more-->

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
