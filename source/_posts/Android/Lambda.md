---
title: Lambda
date: 2016-09-10 13:18:12
tags: [Lambda, Java]
category: Java

---

# Lambda简介

函数式编程中的基础部分

在java8中引入了lambda

Lambda表达式本质是匿名方法，底层通过invokedynamic指令来生成匿名类来实现。

优点：

1. 更简洁的表达方式
2. 数据流的方式处理集合

# Android studio 中加入 lambda

## 方法一
按照[官网教程](https://developer.android.com/guide/platform/j8-jack.html?hl=zh-cn)

Android Studio 版本： 2.2

### 配置Gradle

    android {
      ...
      defaultConfig {
        ...
        jackOptions {
          enabled true
        }
      }
      compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
      }
    }

注意：Instant Run 不能使用 （Instant Run 暂时不支持 jack）

<!--more-->

## 方法二
[https://plugins.gradle.org/plugin/me.tatarka.retrolambda](https://plugins.gradle.org/plugin/me.tatarka.retrolambda)

Gradle 2.1及以上的配置方法：

### 配置

工程的`build.gralde`: buildscript 之下， allprojects 之上

    plugins {
        id "me.tatarka.retrolambda" version "3.3.0"
    }

`app/build.gradle`:

    apply plugin: 'me.tatarka.retrolambda'

    android {
        ...
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
    }

添加 proguard：

    # lambda Gradle Retrolambda Plugin
    -dontwarn java.lang.invoke.*

# 语法

语法结构类似：

    (parameters) -> expression

    or

    (parameters) -> { statements; }

例子：

    new Thread(() -> {
            Log.d(TAG, "onCreate: asdf");
        }).start();

    new Thread(()-> Log.d(TAG, "onCreate: sdfsdfdsf")).start();

Lambda表达式组成部分：

1. paramaters: 方法参数
2. ->:可以理解为‘被用于’
3. 方法体：可以是代码块，也可以是一条语句


<!--more-->


# 使用

## 简化函数式接口

比如上面的Runnable接口

## 方法引用

语法结构

    ObjectRef::methodName

1. 静态方法引用

        mListView.setOnRefreshListener(new OnRefreshListener() {
            @Override
            public void onRefresh() {
                // 呼叫调度台
                CallActivityUtil.startLiveToServicePlatform();
            }
        });

        // Lambda
        mListView.setOnRefreshListener(CallActivityUtil::startLiveToServicePlatform);

2. 实例方法引用
3. 构造方法引用

## Lambda的域及访问限制
....

Predicate

Function

Supplier

Consumer

## Stream

数据流

提供多种操作方法

- 过滤
- 排序
- 映射
- 规约

方法按照返回类型区分为两类：

1. 中间操作
2. 完结操作

Stream使用过程固定的模式：

    创建－－》变化－－》完结

### 中间方法

#### Filter

#### sorted

#### Map

### 完结方法

#### 匹配

#### Collect

#### Count
#### Reduce
### 并行Stream vs 串行Stream

### 懒操作



[http://blog.csdn.net/dd864140130/article/details/50603420](http://blog.csdn.net/dd864140130/article/details/50603420)
