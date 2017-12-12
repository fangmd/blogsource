---
title: 数据结构、算法与应用 c++ 语言描述
date: 2017-12-11 13:18:12
tags: [数据结构, 算法]
category: 读书笔记

---


# 第一章 c++ 回顾

## 引言

## 函数与参数

### 传值参数

```c++
int abc(int a, int b, int c){
    return a + b * c;
}

//...

z = abc(2, x, y)

```

形参（formal parameter）: a, b, c

实参（actual parameter）：2，x，y

传值参数（value parameter）：a，b，c

- 函数运行的时候把形参复制给形参，复制过程是由形参类型的 复制构造函数（copy constructor） 来完成的。类型不同的时候进行强制转换。
- 函数运行结束的时候，形参类型的 析构造函数（destructor） 负责释放形式参数。
- 函数运行结束的时候，形参的值不会被复制到对应的实参中，所以形参不会影响实参值。

### 模版函数


当函数只是改变形参类型的时候，可以通过 模版函数 实现:

```c++
template<class T>

T abc(T a, T b, T c){
    return a + b + c;
}
```

通过模版函数实现函数形参类型的替换。

这种函数调用过程: 当a,b,c是传值参数时，已进入函数，类型T的复制函数便把相应的实参分别复制给形参a，b，c，以供函数使用。 当函数返回时，类型T 的析构函数被启用，以释放形式参数a，b，c的空间。

### 引用参数

引用参数形式的模版函数：

```c++
template<class T>
T abc(T& a, T& b, T& c){
    return a + b + c;
}
```

引用参数（reference parameter）: a,b,c

使用引用参数的函数，在执行和返回的时候，不会执行 复制函数和析构函数。

### 常量引用参数

```c++
template<class T>
T abc(const T& a, const T& b, const T& c){
    return a + b * c;
}
```

const 关键字：指明函数不可修改的引用参数。

更通用的一个模版函数：

```c++
template<class Ta, class Tb, class Tc>
Ta abc(const Ta& a, const Tb& b, const Tc& c){
    return a + b * c;
}
```


### 返回值

返回值可以是：值，引用，常量引用

### 重载函数

## 异常

### 抛出异常

### 处理异常

## 动态存储空间分配

### 操作符 new

### 一维数组

### 异常处理

### 操作符 delete

### 二唯数组

## 自有数据类型

### 类 currency

### 一种不同的描述方法

### 操作符重载

### 友元和保护性类成员

### 增加 #ifndef #define #endif

## 异常类 illegalParameterValue

## 递归函数

### 递归的数学函数

### 归纳

### C++ 递归函数

## 标准模版库

## 测试与调试

### 什么是测试

### 测试数据的设计

### 调试

## 参考及推荐读物



# 第二章 程序性能分析




