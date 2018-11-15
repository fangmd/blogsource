---
title: Gradle知识点
date: 2017-11-07 13:18:12
tags: [Gradle]
category: Android

---

# 依赖相关

## Transitive

Transitive用于自动处理子依赖项。默认为true，gradle自动添加子依赖项，形成一个多层树形结构；设置为false，则需要手动添加每个依赖项。

全部不获取子依赖
```
configurations.all {
   transitive = false
}
```

单独设置 transitive:

```
androidTestCompile('com.android.support.test.espresso:espresso-core:2.1') {
       transitive = false
   }
}
```

## Force

强制设置某个模块的版本

```
configurations.all {
   resolutionStrategy {
       force 'org.hamcrest:hamcrest-core:1.3'
   }
}
```

单独设置

```
compile ('org.greenrobot:eventbus:3.0.0'){
        force = true
}
```

## Exclude

不编译指定子依赖

```
configurations {
   all*.exclude group: 'org.hamcrest', module: 'hamcrest-core'
}
```

```
androidTestCompile('com.android.support.test.espresso:espresso-core:2.1') {
       exclude group: 'org.hamcrest'
   }
}
```

## 查看依赖

### 方法一 推荐

输出依赖到文件

```
./gradlew -q app:dependencies > log
```

### 方法二

通过引入第三方插件来实现

- 首先在应用app的下的build.gradle 文件上加上apply plugin: 'project-report'
- 然后在项目的根目录下执行gradle命令./gradlew htmlDependencyReport 之后会在Build目录下面生成report文件夹，里面生成的有html，里面会有compile的标签，打开即可看到相关的依赖包情况。

此命令有助于查看依赖的版本，因为有的用的低版本，有的高版本，以及查看重复依赖的情况等等，里面还有其他很多的信息可以参考。

参考：

1. [http://www.wxtlife.com/2017/04/16/check-gradle-dependency/](http://www.wxtlife.com/2017/04/16/check-gradle-dependency/)

# gradle 3.0 新内容

## implementation api

[https://stackoverflow.com/questions/44413952/gradle-implementation-vs-api-configuration](https://stackoverflow.com/questions/44413952/gradle-implementation-vs-api-configuration)

原来的 compile 关键词现在变成了 api

implementation: 使用implementation添加的依赖不会再编译期间被其他组件引用到，但在运行期间是完全可见的。这也是一种代码隔离。



# annotationProcessor android-apt

[参考](http://blog.csdn.net/xx326664162/article/details/68490059)

APT(Annotation Processing Tool)是一种处理注释的工具,它对源代码文件进行检测找出其中的Annotation，根据注解自动生成代码。


## annotationProcessor

annotationProcessor是APT工具中的一种，他是google开发的内置框架，不需要引入，可以直接在build.gradle文件中使用，如下

```
dependencies {
     annotationProcessor project(':xx')
     annotationProcessor 'com.jakewharton:butterknife-compiler:8.4.0'
}
```

## android-apt 已经不建议使用了

android-apt是由一位开发者自己开发的apt框架


# Provided annotationProcessor

## annotationProcessor

只在编译的时候执行依赖的库，但是库最终不打包到apk中，
编译库中的代码没有直接使用的意义，也没有提供开放的api调用，最终的目的是得到编译库中生成的文件，供我们调用。

## Provided

Provided 虽然也是编译时执行，最终不会打包到apk中，但是跟apt/annotationProcessor有着根本的不同。
A 、B、C都是Library。 
A依赖了C，B也依赖了C 
App需要同时使用A和B 
那么其中A（或者B）可以修改与C的依赖关系为Provided
A这个Library实际上还是要用到C的，只不过它知道B那里也有一个C，自己再带一个就显得多余了，等app开始运行的时候，A就可以通过B得到C，也就是两人公用这个C。所以自己就在和B汇合之前，假设自己有C。如果运行的时候没有C，肯定就要崩溃了。
总结一下，Provided是间接的得到了依赖的Library，运行的时候必须要保证这个Library的存在，否则就会崩溃，起到了避免依赖重复资源的作用。

