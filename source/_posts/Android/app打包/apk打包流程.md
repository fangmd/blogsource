
---
title: Apk 打包流程
date: 2019-09-05 12:18:12
tags: [apk]
category: Android

---

# APK 包解析

APK 包解压后:

1. `META_INF` 签名文件夹

包含: `CERT.RSA`, `CERT.SF`, `MANIFEST.MF` ...

`MANIFEST.MF`:  版本号，及每个文件的哈希值(SHA-256-Degest)
`CERT.SF`: 版本号，对每个文件的头3行进行 SHA1
`CERT.RSA`: 保存签名和公钥证书

其他：第卅

2. `res` 文件夹

所有的资源文件

3. `AndroidManifest.xml`

Android 清单文件

4. `classes.dex`: dex 文件
5. `resources.arsc`

资源索引表

6. `assets` 静态资源文件夹
7. `kotlin` kotlin 相关的, metadata

## apk 包生成流程

![apk flow](https://upload-images.jianshu.io/upload_images/9984264-bbdece8b63a337fb.png?imageMogr2/auto-orient/strip|imageView2/2/w/536/format/webp)

1. aapt 打包所有 res 资源文件

生成: R.java, resources.arsc, res 文件夹

2. aidl 处理 .aidl 文件

生成对应的 Java 接口文件

3. Java Compiler 阶段

通过 Java Compiler 编译 R.java, Java 接口文件，Java 源文件;

生成 .class 文件

4. dex 阶段

通过 dex 命令，将所有 .class 文件处理成 classes.dex

5. apkbuilder 阶段

将 classex.dex, resources.arsc, res, assets, AndroidManifest.xml 打包成 apk 文件。

6. Jarsigner 阶段

对 apk 进行签名

# Gradle 打包流程

```
sourceGenTask，resGenTask，assetGenTask
checkManifestTask
process the manifest(s) task
create the res values task
compile renderscript files task
merge the resource folders task
merge the asset folders task
create the BuildConfig class task
process the Android Resources and generate source files task
process the java resources task
process the aidl task
compile task
NDK tasks
final packaging task，zipalign task
lint tasks
```

# .class -> dex

dex 文件是一种能被 DVM 识别和并加载，执行的文件格式。(二进制流文件)




具体来说，关于.class文件到dex写入过程主要为：

1. 将class进行类转换处理

2. 将转换后的类写入到dex中(multidex会创建多个dex)

3. 将dex转换成byte[]

4. 将byte[]列表依次写入到classes.dex, classes2.dex, classes3.dex…


参考:

1. [class to dex](http://mouxuejie.com/blog/2016-06-21/multidex-compile-and-dex-source-analysis/)