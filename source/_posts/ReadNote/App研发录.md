---
title: App 研发录
date: 2016-08-07 13:18:12
tags: [Android, 读书笔记]
category: 读书笔记

---

# 高效App框架设计与重构

## 重构，也未眠

### 重新规划Android项目结构

目前自己项目的目录结构：

- data
- module
- net
- widgets
- util
- br
- service
- permission


目的：

- 每个文件只有一个单独类，不要有嵌套
- 讲Activity按照模块拆分，可以迅速定位到具体页面


需要改进的地方：

    bean －－》 Entity

### 为Activity定义新的生命周期

单一职责：一个类或者一个方法，只做一件事。

书中推荐：

    onCreate() 拆分出三个方法：
    initVariables();
    initViews(savedInstanceState);
    loadData();

### 统一事件编程模型

书中推荐在写点击事件的时候使用内部类。

个人推荐使用Butterknife实现点击事件

### 实体化编程

#### 在网络请求中使用实体

书中举了一个反例：讲网络请求返回的数据包装程JSONObject／JSONArray，在项目中直接使用，需要跨Activity传递数据时还将对象封装成全局的。

书中推荐：使用自定义class将网络数据进行转化。配合GSON

需要避免混淆：

    -keepattributes Signature // 不混淆泛型
    -keepattributes *Annotation* // 不混淆注解

#### 实体生成器

推荐一个插件：GsonFormat,利用json数据快速生成实体类

#### 在页面跳转中使用实体类

1. 方法一：对象全全局化

    >书中使用的是使用自定义的一个全局对象来引用实体类对象，app切换到后台的时候，如果内存不足，全局变量就会被回收不推荐使用。
    目前如果App需要有全局参数或者变量，推荐使用自定义的Application对象来引用

2. 方法二：Intent传值

    需要对象实现序列化
    推荐一个序列化插件：Android Parcelable code generator

### Adapter 模版

### 类型安全转换函数

常出现的错误：

1. Object类型对象,对其直接使用字符串操作函数toString，如果为null就会奔溃

        int result = Integer.valueOf(obj.toString());

    解决方法：编写一个类型安全转换函数convertToInt：

        public final static int convertToInt(Object value, int defaultValue){
            if(value==null || "".equals(valur.toString().trim())){
                return defaultValue;
            }
            try{
                return Integer.valueOf(value.toString());
            } catch (Exception e){
                try{
                    return Double.valueOf(value.toString()).intValue();
                } catch (Exception e1){
                    return defaultValue;
                }
            }
        }

    最后使用：

        int result = Utils.convertToInt(obj, 0);

    Utils类中还需要自定义其他的类型转换安全函数。

2. 如果长度不够，substring函数时就会奔溃

    加`try..catch`处理
    不同数据不同处理：1. 不需要加工的数据：数据为空，或者长度符合要求，直接显示。 2. 对于重要数据：比如金额不能为空，应该弹出窗口提示用户

## Android 网络底层框架设计

## Android 经典场景设计

### App 图片缓存设计

书中介绍了ImageLoader,Fresco

本人使用的是Glide

### 对网络流量进行优化

#### 通信层面优化

1. 接口返回的数据压缩：gzip
2. 新的数据传输协议ProtoBuffer
3. 避免频繁发起请求，能一次请求完的就尽量一次解决
4. TCP长连接，提高访问速度
5. **建立取消网络请求机制**
6. 增加重试机制：为get请求建立重试，不要为post请求建立重试

#### 图片策略优化

1. 确保下载的图片，符合ImageView控件的大小

    在请求中设置需要的width，height

2. 低流量模式

    请求中quality

3. 极速模式

### 城市列表的设计
P61
