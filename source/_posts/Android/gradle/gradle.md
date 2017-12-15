---
title: Gradle配置
date: 2016-09-28 13:18:12
tags: [Gradle]
category: Android

---



Android applications 使用 gradle 构建，gradle 是一门高级语言并且广泛用于 java 中，提供的 Android 插件为 Android apps 开发提供了很多的功能，例如 build types, flavors, signing configurations, library projects，更多可查看[Android Plugin DSL Reference](http://google.github.io/android-gradle-dsl/current/index.html)

# Gradle Build Files in Android
理解创建 Android 工程生成的build file

<!--more-->

1. 创建一个Android工程，查看`setting.gradle`,`build.gradle`,`app/build.gradle`

2. 分析

    `settings.gradle` 显示当前项目中有哪些 module

        include ':app'

    progect的`build.gradle`

        `buildscript`节点：是gradle脚本自身需要使用的资源
        `repositories`节点指定下载仓库默认是`jcenter`
        `dependencies`节点申明gradle版本

    `app/build.gradle`

        `apply plugin:'com.android.application'`:作用是把Android插件加入到当前的build工程
        ‘android’节点：一些Android的配置
        ‘dependencies’节点帮助我们添加项目依赖


# Gradle Build Files in Android

## setting project properties

### 问题

如何通过`ext`节点把一些常量从`build.gradle`中移除加入到`gradle.properties`，或者在控制台`-p`设置值。

### 解决方法

- 在`build.gradle`通过`ext`节点把例如版本号，版本名集中设置。

        ext {
          appcompat_version = '24.2.1'
          versionCodes=13
          versionNames='2.3'
        }

        使用：

        defaultConfig {
           applicationId "com.branch.www.gradledemo"
           minSdkVersion 18
           targetSdkVersion 24
           versionCode versionCodes
           versionName versionNames
        }

        或

        compile("com.android.support:appcompat-v7:$appcompat_version")

- 使用gradle.properties

        login='user'
        pass='my_long_and_highly_complex_password'

    使用：

        repositories {
            maven {
                url 'http://repo.mycompany.com/maven2'
                credentials {
                username 'user'
                password 'password'
            }
         }
        }

# 了解 build Types 和 Flavors

## Build Types
- debug，release
- minifyEnables，shrinkResources
    开发很久的项目中通常会存在很多已经不再使用的资源文件，但是人工去删除又比较麻烦，可以使用这两个帮助删除不再使用的资源文件。

        buildTypes {
        release {
        minifyEnabled true
        shrinkResources true
        proguardFiles getDefaultProguardFile('proguard-android.txt'),Turn on code shrinking'proguard-rules.pro'
        } }


- 设置后缀属性
    为了方便辨别打包后的包是release或debug，可以根据不同的打包类型设置区分。如修改版本名称，修改包名。

        android {
        // ... other properties ... buildTypes {
                debug {
                    applicationIDsuffix '.debug'
                    versionNameSuffix '-debug'
        }
        // .. other build types ...
        } }

## Flavors and Variants

### 问题

如何做到不同一次build同一个APP不同版本功能的包。

### 解决方法

productFlavors用于build不同版本的包，他可能用于免费版，收费版，但是基于代码是一样的，只是有个别属性是不一样的。

    productFlavors {
            baidu {
              applicationId 'com.branch.www.gradledemo.baidu'
            }
            meizu {
              applicationId 'com.branch.www.gradledemo.meizu'
            }
            wandoujia {
              applicationId 'com.branch.www.gradledemo.wandoujia'
        }

build 的时候就会出现很多包

### 利用这个设置 debug minSdkVersion21 加快编译速度方便开发

    productFlavors {
        // Define separate dev and prod flavors
        dev21 {
            minSdkVersion 21
        }
        dev16 {
            minSdkVersion 16
        }
        prod {
            // The actual minSdkVersion for the production app
            minSdkVersion 16
        }
    }

build-》Select Build Variants 设置debug的 minSdkVersion。



参考：
- [http://www.jianshu.com/p/cfa802396c6a](http://www.jianshu.com/p/cfa802396c6a)
- [http://www.jianshu.com/p/1182b1244677](http://www.jianshu.com/p/1182b1244677)
- [http://www.jianshu.com/p/5e28100aadea](http://www.jianshu.com/p/5e28100aadea)
