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

<!--more-->

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

2. begin and end operator

begin: 返回指向第一个元素的迭代器：

```
vector<int>::iterator iter = ivec.being();
```

end：返回指向最后一个元素的迭代器。

3. 迭代器指向的移动

```
*iter = 0 : 使用 解引用操作符 来访问迭代器所指向的元素
++iter：指向第二个元素
--iter：指向前一个元素
```

使用索引遍历集合：

```c++
for(vector<int>::size_type ix = 0; ix != ivec.size(); ++ix){
	ivec[ix] = 0;
}
```

for 循环遍历，将每个元素都赋值为 0

使用迭代器遍历集合：

```c++
for(vector<int>::iterator iter = ivec.being(); iter != ivec.end(); ++iter){
	*iter = 0;
}
```

处理完成最后一个元素后，iter再增加1，就会与 end 操作的返回值相等。


4. 只读迭代器 const_iterator


5. 迭代器算数操作

```
vector<int>:: iterator mid = vi.begin() + vi.size()/2;
```


## 标准库 bitset 类型

```
#include<bitset>
using std::bitset
```

二进制位的有序集合

### bitset 对象的定义和初始化

```
bitset<32> bitvec; 	// 32 bits, all zero
```


### bitset 对象上的操作

# 第四章 数组和指针

C++ 语言提供了两种类似 verctor 和 迭代器类型 和低级复合类型 -- 数组和指针。

>尽量使用 vector和迭代器类型

数组长度固定。

## 数组

### 数组的定义和初始化

数组定义的时候必须指定 维数(长度)，并且 维数 必须是常量：

```
const unsigned buf_size = 512, max_files = 20;
int staff_size = 27;
const unsigned sz = get_size();

char input_buffer[buf_size];	// ok
string fileTable[max_files + 1]	// ok
double salaries[staff_size];	// error
int test_scores[get_size()];	// error
int value[sz];	// error size not known until run time
```

1. 显示初始化数组元素

```c++
const unsigned array_size = 3;
int ia[array_size] = {0, 1, 2};

int ia2[] = {0, 1, 2}
```

- 在函数体外的定义的内置数组，其元素均初始化为 0
- 在函数体内定义的内置数组，其元素无初始化
- 如果元素为类类型，不管数组在哪里定义 都会使用默认构造函数进行初始化；如果该类没有默认初始化函数，必须为该数组的元素提供显式初始化。

显示初始化数组的时候不需要制定数组的维数(长度)值。

- 指定长度 必须 大于等于 初始化的显示元素个数。

2. 特殊的字符数组

3. 不允许数组直接复制和赋值

```c++
int ia[] = {0, 1, 2}
int ia2[]{ia}; // error 复制

int ia3[3];
ia3 = ia;	// error 赋值
```

### 数组操作


使用下标操作, vector 使用 `vector::size_type` 作为下标， 数组下标类型是 `size_t`

```c++
int ia[10];

for(size_t ix = 0; ix != 10; ++ix){
	ia[ix] = ix;
}
```


## 指针的引入

解引用操作符 dereference operator: `*`


### 什么是指针

指针保存的是另一个对象的地址。

去地址符号 address-of：`&`

```c++
string s("Hello, world");
string *sp = &s;
```

第二条语句：定义了一个指向 string 类型的指针 sp，初始化 sp 指向 对象s，`*`操作符表示 sp 是一个指针变量，&s 取地址。

### 指针的定义和初始化

1. 指针变量的定义

```c++
vector<int> *pvec
int *op1, *ip2;
```

2. 另一种声明指针的风格

```c++
string* ps;	// legal but can be misleading
```

4. 指针可能的取值

指针的三种状态：

- 保存一个特定对象的地址
- 指向某个对象后面的另一个对象
- 0 值，不指向任何对象

```c++
int ival = 1024;
int *pi = 0;	// pi initialized to address no object
int *pi2 = &ival; // pi2 initialized to address of ival
int  *pi3;	// ok 没有初始化的指针，

pi = pi2;
pi2 = 0; // pi2 now address no object
```

5. 避免使用未初始化的指针

7. void 指针：可以指向任何类型


### 指针的操作


```c++
string s("hello world");
string *sp = &s;
cout << *sp; // prints hello world

*sp = "goodbye"	// 修改 s 的值

string s2 = "some value";
sp = &s2;	// sp 指向 s2
```

3. 指向指针的指针

```c++
itn ival = 1024;
int *pi = &ival;
int **ppi = &pi;
```

### 使用指针访问数组元素

指针和数组密切相关，在表达式中使用数组名的时候，名字会自动转换为指向数组第一个元素的指针。

```c++
int ia[] = {0, 2, 3, 4, 5};
int *ip = ia;	// ip points to ia[0]

ip = &ia[4];	// ip points to last element in ia
```

1. 指针算数

```c++
ip = ia;

int *ip2 = ip + 4; 	//ok: ip2 points to ia[4]


ptrdiff_t n = ip2 - ip1;	// ok, distance between the pointers
```


6. 指针是数组的迭代器

```c++
for(vector<int>::iterator iter = ivec.begin(); iter != ivec.end(); ++iter){
	*iter = 0;
}
```


### 指针和 const 限定符

1. 指向 const 对象的指针

```c++
const double *cptr;

*cptr = 42;	// error: *cptr migth be const, 不能通过 cptr 修改其所指对象的值
```

不能把一个 const 对象的地址赋给一个普通的对象指针：

```c++
const double pi = 3.14;
double *ptr = &pi;	// error
const double *cptr = &pi;	// ok
```

2. const 指针

```c++
int errNum = 0;
int *const curErr = &errNum;
```


## C 风格字符串

>C++ 支持 c 风格的字符串，但是不应该在 C++ 中使用这个类型，容易出错。

### 创建动态数组

### 新旧代码的兼容

### 多维数组

# 第五章 表达式

表达式由 多个操作数和操作符 而成。

## 算术操作符

```
+, -, *, /, %
```

## 关系操作符和逻辑操作符

```
! < <= > > >= == != && ||
```

## 位操作符

```
~ << >> & ^ |
```

### bitset 对象或整型值的使用

### 将移位操作符用于 IO

```
cout << "hi" << " there" << endl;
```

## 赋值操作符

```
=
```

### 赋值操作的右结合性

### 赋值操作具有低优先级

### 复合赋值操作符

```
+= -= *= /= 
<<= >>= &= ^= |=
```

## 自增和自减操作符

```
++i
i++

--i

i--
```

## 箭头操作符

```
->
```

## 条件操作符

唯一的 三元操作符

```
cond ? expr1 : expr2;
```

## sizeof 操作符

返回一个对象或类型名的长度，返回值类型为 size_t.

```
sizeof(type name);
sizeof(expr);
sizeof expr;
```

## 逗号操作符

## 复合表达式的求值

### 优先级

### 结合性

### 求值顺序

## new 和 delete 表达式

```c++
int *pi = new int();

delete pi;


int i;
int *pi = &i;
string str = "dwareves";
double *pd = new double(33);

delete str;	// error: str is not a dynamic object
delete pi;	// error pi refers to a local
delete pd;	// ok
```

>如果指针指向不是用 new 分配的内存地址，则在该指针上使用 delete 是不合法的。


6. delete 之后，重设指针的值

```
delete p;
```

删除指针后，该指针变成悬垂指针(dangling pointer)

7. const 对象的动态分配和回收

## 类型转换

### 何时发生隐式类型转换

1. 混合类型的表达式中

```c++
int ival;
double dval;

ival >= dval	// ival converted to double
```

2. 用作条件的表达式转换为 bool 类型

```c++
int ival;
if(ival)	// ival converted to bool
```

3. 用一表达式初始化某个变量，或者将一表达式赋值给某个变量，该表达式被转换成该变量的类型；

```c++
int ival = 3.24;	// 3.14 converted to int
int *ip;
ip = 0;	
```

### 算术转换

类型提升

### 其他隐式转换

### 显式转换

### 何时需要强制类型转换

### 命名的强制类型转换

### 旧式强制类型转换


# 语句

## 简单语句

大多数语句以分号结束。

空语句：在有些地方语法上需要一个语句，但是逻辑上并不需要。

```c++
;


while(cin >> s && s != sought){
	;
}
```

## 声明语句

## 复合语句

## 语句作用域

## if 语句

```c++
if(condition){

}

if(condition){

}else{

}
```

## switch 语句

### 使用 switch


```c++
switch(ch){
	case 'a':
		break;
	case '0':
		break;
	default:
		break;
}
```

### switch 中的控制流

c++ 中 switch 语句具有穿透性，需要添加 break

### default 标号

### switch 表达式与 case 标号

### switch 内部的变量定义

## while 语句

```c++
while(condition){

}
```

## for 循环语句

```c++
for(init-statement; condition; expression){

}
```

### 省略 for 语句头的某些部分

### for 语句头中的多个定义

## do while 语句

```c++
do{

}while(int foo == get_foo());
```

## break 语句

跳出最近的循环

## continue 语句

跳出本次循环，继续下次循环

## goto 语句

```c++
begin:
int sz = get_size();
if(sz <= 0){
	goto begin;
}
```

## try 快和异常处理

### throw 表达式

```c++
throw runtime_error(".....")
```

### try 块

```c++
try{
	//...
}catch (exception-specifier){

}
```

### 标准异常

```
exception

runtime_error
range_error
overflow_error
```

## 使用预处理器进行调试

```c++
int main(){
	#ifndef NDEBUG
	cerr << "starting main"	<< endl;
	#endif
	//...
}
```


# 第七章 函数

## 函数的定义

 函数由函数名以及一组操作数类型唯一地表示。

 形参

 函数体

 返回类型

1. 函数调用的时候

主调函数（calling function）的执行被挂起，被调函数（called function）开始执行。

2. 函数体是一个作用域

局部变量（local variable）

### 函数返回类型

### 函数形参表

## 参数传递

### 非引用形参

```c++
int gcd(int v1, int v2){
	while(v2){
		int temp = v2;
		v2 = v1 % v2;
		v1 = temp;
	}
	return v1;
}

gcd(i, j)
```

i, j 的值不会受到函数执行而改变。


1. 指针形参

```c++
void reset(int *ip){
	*ip = 0;	// 可以更改 ip 指针指向的值
	ip = 0;	// 不能改变 ip 指针指向的值
}

// 使用 const 避免改变

void use_ptr(const int *p){
	// *p 只读
}
```

2. const 形参数


### 引用形参

```c++
void swap(int &v1, int &v2){
	int tmp = v2;
	v2 = v1;
	v1 = tmp;
}
```

### vector 和 其他容器类型 的形参

### 数组 形参

### 传递给函数的数组的处理

### main： 处理命令行选项

### 含有可变形参的函数

## return 语句

### 没有返回值的函数

### 具有返回值的函数

1. main 的返回值

0: 表示程序运行成功

非0: 表示程序运行有问题

2. 返回非引用类型

临时对象 temporary object

3. 返回引用

4. 千万不要返回局部对象的引用

```c++
const string &manip(const string &s){
	string ret = s;
	return ret;	// wrong: returing reference to a local object
}
```

6. 千万不要返回指向局部对象的指针

局部对象被释放后，执政变成了 悬垂指针

### 递归

```c++
int factorial(int val){
	if(val > 1){
		return factorial(val - 1) * val;	
	}
	return;
}
```

## 函数声明

函数的声明可以和函数的定义分离；一个函数只能定义一次，但是可以声明多次。

```c++
void print(int *array, int size);
```

在头文件提供函数的声明。

## 局部对象

### 自动对象

自动对象：当定义它的函数被调用的时才存在的对象

### 静态局部对象

static 局部对象：延长对象的声明周期

```c++
size_t count_calls(){
	static size_t ctr = 0;
	return +=ctr;
}

int main(){
	for(size_t i = 0;  i != 10; ++i){
		cout << count_calls() << endl;	
	}
	return 0;
}

// 程序依次输出 1 到 10 整数。
```


## 内联函数



## 类的成员函数

### 定义成员函数的函数体

### 在类外定义成员函数

### 编写 Sales_item 类的构造函数

### 类代码文件的组织

## 重载函数

### 重载与作用域

### 函数匹配与实参转换

### 重载确定的三个步骤

### 实参类型转换

## 指向函数的指针

# 第八章 标准 IO 库








