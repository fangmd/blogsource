---
title: Gradle知识点
date: 2017-11-07 13:18:12
tags: [Gradle]
category: Android

---

# gradle 3.0 新内容

## implementation api

[https://stackoverflow.com/questions/44413952/gradle-implementation-vs-api-configuration](https://stackoverflow.com/questions/44413952/gradle-implementation-vs-api-configuration)

原来的 compile 关键词现在变成了 api

implementation: 不会对外暴露该库




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