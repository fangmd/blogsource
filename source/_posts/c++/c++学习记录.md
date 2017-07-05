---
title: c++ 学习记录
date: 2016-04-03 13:18:12
tags: c++
category: android

---

# C++ 学习记录

## 修饰符类型
- signed
- unsigned
- long
- short

<!--more-->

## 类型限定符
- const
- volatile
- restrict

## 存储类
- auto
- register
- static
- extern
- mutable

### auto
所有局部变量默认的存储类

### register
register 存储类用于定义存储在寄存器中而不是 RAM 中的局部变量。

### static
static 存储类指示编译器在程序的生命周期内保持局部变量的存在，而不需要在每次它进入和离开作用域时进行创建和销毁。

static 修饰符也可以应用于全局变量。当 static 修饰全局变量时，会使变量的作用域限制在声明它的文件内。

### extern
extern 存储类用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。

### mutable
mutable 说明符仅适用于类的对象，这将在本教程的最后进行讲解。它允许对象的成员替代常量。也就是说，mutable 成员可以通过 const 成员函数修改。

## 运算符
- 算术运算符
- 关系运算符
- 逻辑运算符
- 位运算符
- 赋值运算符
- 杂项运算符

## 循环
- while
- for
- do。。while
- 嵌套


### 循环控制语句
- break
- continue
- goto

## 判断

### 判断语句
- if
- if 。。 else
- 嵌套 if
- switch
- 嵌套switch

### 运算符 ？ ：

## 函数

### 定义函数
- 返回类型
- 函数名称
- 参数
- 函数主体


### 函数声明
函数声明会告诉编译器函数名称及如何调用函数。

	return_type function_name( parameter list  );

在函数声明中，参数的名称并不重要，只有参数的类型是必需的，因此下面也是有效的声明：

	int max(int, int);

当您在一个源文件中定义函数且在另一个文件中调用函数时，函数声明是必需的。在这种情况下，您应该在调用函数的文件顶部声明函数。

### 调用函数

### 函数参数
- 传值调用
- 指针调用
- 引用调用


### 参数的默认值

	int sum(int a, int b=20)
	{
		int result;

		result = a + b;
      
        	return (result);

	}

## 数字
int，short，long，float，double


### 数学运算








