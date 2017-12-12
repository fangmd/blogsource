---
title: c++ Primer4th 笔记
date: 2017-12-12 13:18:12
tags: c++
category: 读书笔记

---


# 第一章 快速入门

- C++ 基本要素：内置类型，库类型，类类型，变量，表达式，语句和函数
- 如何编译和运行程序


## 编写简单的 C++ 程序

```c++
int main(){
	return 0;
}
```

- main 函数作为 c++ 程序的入口函数
- main 函数是唯一被操作系统显式调用的函数

函数4元素：返回类型，函数名，圆括号内的形参表，函数体


**编译与执行程序**

编译器：CNU 编译器／微软编译器

**编译到运行完整流程：**

`hello.cpp`:

```c++
#include<iostream>

int main(){
	std::cout << "Hello, World!1111" << std::endl;
	return 0;
}
```

编译生成文件：`a.out`:

```
g++ hello.cpp
```

执行程序:

```
./a.out
```


## 初窥输入／输出

### 标准输入与输出对象

标准库的 4 个 IO 对象：

- cin (see-in): 标准输入
- cout (see-out): 标准输出
- cerr(see-err): 标准错误
- clog(see-log): log

### 一个使用 IO 库的程序

1. 写入到流

输入操作符（<<）

```
std::cout << "Enter two numbers:" << std::endl;
```

endl: 操纵符（manipulator），将它写入输出流时，具有输出换行的效果，并刷行与设备相关联的缓冲区（buffer).

2. 使用标准库中的名字

添加 命名空间（namespace），可以直接使用 cout

3. 读入流

```
std::cin >> v1 >> v2

// equal to

std::cin >> v1;
std::cin >> v2;
```


## 关于注释

comment

```c++
/* comments */
```

- 注释不可以嵌套

## 控制结构

### while 语句

```c++
#include<iostream>
int main(){
	int sum = 0, val = 1;
	while(val <= 10){
		sum += val;
		++ val;
	}
	std::cout << "Sum of 1 to 10 inclusive is"
				<< sum << std::endl;
	return 0;
}
```

### for 语句

```c++
int sum = 0;
for(int val = 1; val <= 10; += val){
	sum += val;
}
```

### if 语句

```c++
int v1, v2;
int lower, upper;

if(v1 <= v2){
	lower = v1;
	upper = v2;
}else{
	lower = v2;
	upper = v1;
}
```

### 读入未知数目的输入

```c++
#include<iostream>
int main(){
	int sum = 0, value;
	while(std::cin >> value){
		sum += value;
	}
	std::cout << "Sum is" << sum << std::endl;
	return 0;
}
```

执行后结束：mac-os `ctrl-d`, windows: `ctrl + z`

## 类的简介

class type

### Sales_item 类（不看）

存储 ISBN书 的销售册数，销售收入和平均售价。

### 初窥成员函数 （不看）

## C++ 程序


# 第二章 变量和基本类型

## 基本内置类型

- bool
- char	8
- wchar_t	16
- short	16
- int 	16
- long 	32
- float  	6	
- double	10
- long double 	10	


### 整型

signed:

- int 
- short
- long

unsigned: 

- usigned long 
- usigned int


### 浮点型

## 字面值常量

literal constant

## 变量

### 什么是变量

**左值和右值：**

- 左值 lvalue: 左值可以出现在赋值语句的左边或右边
- 右值 rvalue：右值只能出现在赋值语句的右边


变量是左值。

### 变量名

标识符 identifier：变量名，由字母，数字，下划线组成，必须由字母或下划线开头，区分大小写。

### 定义对象

类型说明符 type specifier

1. 初始化

复制初始化 copy-initialization `int ival = 1024;`

直接初始化 direct-initialization `int ival(1024);`

### 变量初始化规则

1. 内置类型变量的初始化

内置类型变量是否自动初始化取决于变量定义的位置。

- 函数体外的变量都初始化为 0
- 函数体内的变量不会自动初始化

2. 类类型变量的初始化

默认构造函数 default constructor

### 声明和定义

- 定义 definition: 用于为变量分配存储空间，可以为变量指定初始值
- 声明 declaration: 用于向程序表明变量的类型和名字

```
extern int i; // declares but does not define i

int i;	// declares and defines i
```

只声明不会分配内存空间，如果声明的同时定义就会分配内存空间：

```
extern double pi = 3.1416;	// definition
```

### 名字的作用域

scope

- global scope: 全局作用域
- local scope: 局部作用域
- statement scope: 语句作用域


### 在变量使用处定义变量

>通常把一个对象定义在它首次使用的地方是很好的方法

## const 限定符

const: 把一个对象转化成一个常量。

```
const int bufSize = 512;
```

const 对象默认为文件的局部变量

## 引用

reference

```
int ival = 1024;
int &refVal = ival;
int &refVal2;
int &refVal3 = 10;
```

1. 引用是别名

引用只是它绑定的对象的另一个名字，作用在引用上的所有操作事实上都作用在该引用绑定的对象上。

2. const 引用

const 对象的引用只能是 const 引用：

```
const int ival = 1024;
const int &refVal = ival;	// is ok
int &ref2 = ival;	// is wrong
```

## typedfe 名字

```
typedef double wages;
```

wages 类型代表 double.

目的：

1. 为了隐藏特定类型的实现，强调使用类型的目的。
2. 简化复杂的类型定义，使其更易理解
3. 允许一种类型用于多个目的，同时使得每次使用该类型的目的明确

## 枚举

1. define

```c++
enum open_modes{ input, output, append};

// input = 0, output = 1, append = 2

enum open_modes{ input = 1, output, append};

// input = 1, output = 2, append = 3
```

2. 枚举成员是常量

3. 每个 enum 都定义一种唯一的类型


## 类类型

```c++
class Sales_item{
	public:

	private:
		std::string isbn;
		unsigned units_sold;
		double revenue;
};
```


**使用 struct 关键字**

```c++
struct Sales_item{

private:
	std::string isbn;
	unsigned units_sold;
	double revenue;
};
```

struct 与 class 区别：struct 修饰的类 成员默认访问级别都是 public.

> struct 成员为 public, class 成员为 private.


## 编写自己的头文件

一般类的定义都会放在 header file 中。


### 设计自己的头文件

1. 头文件用于声明而不是定义
2. 一些 const 对象定义在头文件中

### 预处理器的简单介绍

`#include` 属于 C++预处理器 的一部分

1. 头文件经常需要其他头文件

	使用 头文件保护符（header guard)，避免类和对象多次被定义
2.  避免多重包含

```
#ifndef SALESITERM_H
#define SALESITEM_H

#endif
```


`#ifndef SALESITERM_H` 检测 SALESITERM_H 是否已经被定义，如果已经被定义就不会执行下面的代码了。

3. 使用自定义的头文件

```
#include <standard_header>
#include "my_file.h"
```


# 第三章 标准库类型

## 命名空间的 using 声明

一次，只能引入一个名字，

```
using namespace::name
```

1. 每个名字都需要一个 using 声明

## 标准库 string 类型

```
using std::string
```

### string 对象的定义和初始化

初始化：

```
string s1;
stirng s2(s1);
sting s3("value");
string s4(n, 'c')
```

### string 对象的读写

### string 对象的操作

```
s.empty() ： retrun true/false
s.size()
s[n]
s1 + s2
s1 = s2
v1 == v2
!=, <, <=
> 和 >=
```

### string 对象中字符的处理

单个字符的处理，`#include <cctype>` 中有大量的处理方法：

```
isalnum(c)	判断是否是字母或者数字
isalpha(c)	如果c是字母，返回 true
iscntrl(c)	如果c是控制字符，返回 true
isdigit(c)	如果c是数字，则为true

isspace(c)
isupper(c)


tolower(c)
toupper(c)
```

## 标准库 vector 类型

vector 集合，容器

```
#include <vector>
using std::vector;
```

### vector 对象的定义和初始化

```
vector<T> v1;
vector<T> v2(v1);
vector<T> v3(n, i);
vector<T> v4(n)
```


### vector 对象的操作

```
v.empty()
v.size()	
v.push_back(t)	在v末尾增加一个值为 t 的元素
v[n]
v1 = v2
v1 == v2
!=, <=, >, >=
```

3. vector 的下标操作：

```
for(vector<int>::size_type ix = 0; ix != ivec.size(); ++ix){
	ivec[ix] = 0;
}
```

- 注意这里使用的是 `!=`


## 迭代器简介

iterator

1. 容器的 iterator 类型

```
vector<int>::iterator iter;
```

## 标准库 bitset 类型

### bitset 对象的定义和初始化

### bitset 对象上的操作

# 第四章 数组和指针







