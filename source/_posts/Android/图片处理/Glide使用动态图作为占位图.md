---
title: Glide 使用动态图作为占位图
date: 2016-11-15 21:38:14
tags: Glide
categories: android

---


# 完整的例子

## 布局
ImageView

    <ImageView
        android:id="@+id/iv_main"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_alignParentBottom="true"
        android:scaleType="fitXY"
        />

## 创建一个 drawable

`R.drawable.rotate_pro`

    <?xml version="1.0" encoding="utf-8"?>
    <rotate
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:drawable="@drawable/group_1"
        android:fromDegrees="0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toDegrees="360"/>

## Activity 中代码

    ImageView img = (ImageView) findViewById(R.id.iv_main);

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

## 最终效果：

![占位图](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/glide_place_holder.gif)

## 水平loading动画
替换上面使用的 drawable 即可

    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
        <item>
            <clip
                android:clipOrientation="horizontal"
                android:drawable="@drawable/rectangle"
                >

            </clip>
        </item>

    </layer-list>

效果：

![占位图](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/glde_place_holder_clip.gif)
