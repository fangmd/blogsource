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

```c++
const string &shorterString(const string &s1, const string &s2){
	return s1.size() < s2.size() ? s1 : s2;
}
```

为这样的小操作定义一个函数的优点：

1. 阅读和理解函数比读一条表达式要轻松
2. 方便修改，修改函数比修改所有的表达式要简单
3. 函数可以重用

缺点：

1. 慢

内联函数可以避免上面的缺点，编译的时候会自动拆解函数：

```c++
cout << shortString(s1, s2) << endl;

// 编译时会展开：
cout << (s1.size() < s2.size() ? s1 : s2) << endl;
```

内联函数：

```c++
inline const string &shorterString(const string &s1, const string &s2){
	return s1.size() < s2.size() ? s1 : s2;
}
```

内联机制适用于优化小，只有几行并且经常调用的函数。

**内联函数放入头文件**

内联函数的定义对编译器而言必须是可见。


## 类的成员函数


```c++
class Sales_item{
	public:
		double avg_price() const;
		bool same_isbn(const Sales_item &rhs) const{
			return isbn == rhs.isbn;
		}

	private:
		std::string isbn;
		unsigned unis_sold;
		double revenue;
}
```



### 定义成员函数的函数体


1. 成员函数含有额外的，隐含的形参数

2. this 指针的引入
	每个成员函数都有一个额外的，隐含的形参 this。

3. const 成员函数的引入



### 在类外定义成员函数

在类的定义外面定义成员函数必须指明它们是类的成员：

```c++
double Sales_item::avg_price() const{
	if(units_sold){
		return revenue/units_sold;
	}else{
		return 0;
	}
}
```

### 编写 Sales_item 类的构造函数

1. 构造函数是特殊的成员函数
	构造函数和类同名，没有返回值
	默认构造函数

2. 构造函数的定义

```c++
class Sales_item{
	public:
		double avg_price() const;
		bool same_isbn(const Sales_item &rhs) const{
			return isbn == rhs.isbn;
		}

		Sales_item():units_sold(0), revenue(0.0){

		}

	private:
		std::string isbn;
		unsigned unis_sold;
		double revenue;
}
```

3. 构造函数的初始化列表


### 类代码文件的组织

类 Sales_item 放在名为 Sales_item.h 文件中定义。

成员函数的定义在 Sales_item.cc 文件中。

## 重载函数

### 重载与作用域

### 函数匹配与实参转换

### 重载确定的三个步骤

### 实参类型转换

## 指向函数的指针

# 第八章 标准 IO 库

## 面向对象的标准库

IO 标准库类型和头文件

- iostream：istream，ostream，iostream
- fstream：ifstream，ofstream，fstream
- sstream：istringstream，stringstream

1. 国际字符的支持

`wchar_t` 类型

2. IO 对象不可复制或赋值

```c++
ofstream out1, out2;
out1 = out2;	// error, cannot assign stream objects
```


## 条件状态

IO 标准库的条件状态：

- strm::iostate
- strm::badbit
- strm::failbit
- strm::eofbit
- s.eof()
- s.fail()
- s.bad()
- s.good()
- s.clear()
- s.clear(flag)
- s.setstate(flag)
- s.rdstate()


## 输出缓冲区的管理

每个 IO 对象管理一个缓存区，用于存储程序读写的数据，缓冲区刷新触发条件：

1. 程序结束。main 函数结束
2. 缓存区满的时候
3. 使用操纵符显式刷新缓冲区，比如：endl
4. 在每次输出操作执行完成后，用 unitbuf 操纵符设置流的内部状态，从而清空缓冲区
5. 输出流与输入流关联(tie)的时候，在读输入流时将刷新关联的输出缓冲区

**输出缓冲区的刷新**

```
cout << "hi" << flush;	// flushes the buffer

cout << "hi" << ends;	// inserts a null, flushes the buffer

cout << "hi" << endl;	// inserts a newline, then flushes the buffer
```

3. unitbuf

刷行所有输出

```
cout << unitbuf << "first" << " second" << nounitbuf

// equals

cout << "first" << flush << " second" << flush;
```

>如果程序奔溃，则不会刷新缓冲区


3. 将输入和输出绑定在一起



## 文件的输入和输出

### 文件流对象的使用

```c++
// construct an ifstream and bind it to the file named ifile
ifstream infile(ifile.c.str());

// ofstream output file object write file named ofile
ofstream outfile(ofile.c_str());
```

1. 检查文件是否打开成功

```c++
if(!infile){
	cerr << "Error: unable to open input file:"
		<< ifile << endl;
	return -1;
}
```

```
if(outfile) 	// ok, to use outfile
```

2. 将文件流与新文件重新捆绑

fstream 对象打开就会与指定文件关联，如果要与另一个文件关联，需要先关闭之前的。

```c++
ifstream infile("in");
infile.close();
infile.open("next");
```

3. 清除文件流的状态

```c++
ifstream input;
vector<string>::const_iterator it = files.begin();

while(it != files.end()){
	intput.open(it -> c_str());	// open file
	if(!input){
		break;
	}
	while(intput >> s){
		process(s);
	}
	input.close();
	input.clear();	// reset state to ok
	++it;

}
```


### 文件模式

- in
- out
- app
- ate
- trunc
- binary

### 一个打开并检查输入文件的程序

## 字符串流

# 第 9 章 顺序容器

顺序容器：

- vector
- list
- deque (double-ended queue)

顺序容器适配器：

- stack: LIFO
- queue: FIFO
- priority_queue: 有优先管理的队列

## 顺序容器的定义

```c++
vector<string> svec;
list<int> ilist;
deque<Sales_item> items;
```

### 容器元素的初始化

1. 容器初始化的时候可以使用另一个相同类型的容器副本
2. 初始化为一段元素的副本

```c++
list<string> slist(svec.begin(), svec.end());
```

3. 分配和初始化指定数目的元素

### 容器内元素的类型约束

## 迭代器和迭代器范围

常用迭代器运算：

```
*iter 	返回迭代器 iter 所指向的元素的引用
iter -> mem	, (*iter).mem
++iter
iter++
--iter
iter--
iter1 == iter2
iter1 != iter2

```

### 迭代器范围

c++ 使用一对迭代器标记迭代器范围（iterator range）

包头不包尾

[first, end)

**使用左闭合区间的变成意义：**

- 当 first, last 相等时，迭代器范围为空
- 当 first, last 不相等, 迭代器范围内至少有一个元素，可以通过下面的代码写循环

```c++
while(first != last){
	+=first;
}
```

### 使迭代器失效的容器操作

## 顺序容器的操作

### 容器定义的类型别名

- size_type
- iterator
- const_iterator
- reverse_iterator
- const_reverse_iterator
- difference_type
- value_type
- reference
- const_reference

### begin 和 end 成员

begin and end 指向容器内的第一个元素和最后一个元素的下一位置的迭代器。

```
c.begin()

c.end()

c.rbegin(): 返回一个逆序迭代器，它指向容器 c 的最后一个元素

c.rend(): 返回一个逆序迭代器，它指向容器 c 的第一个元素的前面的位置
```

### 在顺序容器中添加元素

```
c.push_back(t)
c.push_front(t)

// 下面只适用于 list, deque
c.insert(p, t)
c.insert(p, n, t)
c.insert(p, b, e)
```


### 关系操作符

```
ivec1: 1 3
ivec2: 0 2 4

ivec1 > ivec2 // 第一个元素就能比较出大小
```

### 容器大小的操作

```c++
c.size()

c.max_size()

c.empty()

c.resize(n)

c.resize(n, t)
```

### 访问元素

```
c.back()
c.front()

// 下面两个适用于 vector deque
c[n]
c.at(n)

```

### 删除元素

```
c.erase(p)
c.erase(b, e)
c.clear()
c.pop_back()
c.pop_front()
```

### 赋值与 swap

```
c1 = c2	删除容器 c1 的所有元素，然后将 c2 的元素复制给 c1. c1 和 c2 的类型必须相同

c1.swap(c2) 交换内容

c.assign(b, e)
c.assign(n, t)	将容器 c 重新设置为存储 n 个值为 t 的元素

```

## vector 容器的自增长

vector 容器的元素以连续的方式存储元素。（数组数据结构）

**capacity, reserve 成员**：capacity 操作获取在容器需要分配更多存储空间之前能够存储的元素总数，而 reserve 操作则告诉 vector 容器应该预留多少个元素的存储空间。

>每当 vector 容器不得不分配新的存储空间时，以加倍当前容量的分配策略实现重新分配。


## 容器的选用

list 容器：内存区域不连续，允许向前和向后遍历元素。插入和删除效率高，不支持随机访问，访问某个元素必须遍历所有相关元素。

vector：除了尾部外，其他位置插入／删除数据都需要移动其他数据效率低，内存区域连续

deque：从两端插入和删除元素都很快，但是在中间插入和删除元素效率低。

## 再谈 string 类型



### 构造 string 对象的其他方法

### 修改 string 对象的其他方法

### 只适用于 string 类型的操作

### string 类型的查找操作

### string 对象的比较

## 容器适配器

- queue
- priority_queue
- stack

适配器：一个事物转化成另一个事物的机制。

适配器通用的操作和类型：

```
size_type

value_type

container_type

A a;

A a(c);

关系操作符
```

### 栈适配器

栈容器适配器支持的操作：

```
s.empty()
s.size()
s.pop()
s.top()
s.push(item)
```

```c++
const stack<int>::size_type stk_size = 10;
stack<int> intStack;	// empty stack

// fill up the stack
int ix = 0;
while(intStack.size() != stk_size){
	intStack.push(ix++);
}
int error_cnt = 0;

// look at each value and pop it off the stack
while(intStack.empty == false){
	int value = intStack.top();
	// read the top element of the stack
	if(value != --ix){
		cerr << "oops! expected" << ix
			<< "received " << value << endl"

		++error_cnt;
	}
	intStack.pop();	// op the top element, and repeat
}

cout << "Our program ran with "
	<< error_cnt << " errors!" << endl;
```



### 队列和优先级队列

队列和优先级队列支持的操作：

```
q.empty()
q.size()
q.pop()
q.push(item)

q.front()	// 只适用于队列
q.back()	// 只适用于队列

q.top()	// 只适用于优先级队列
```


# 第十章 关联容器

associative container

```
map 	关联数组，元素通过键来存储和读取
set 	大小可变的集合，支持通过键实现快速读取
multimap 	支持同一个键多次出现的 map
multiset	支持同一个键多次出现的 set
```

## 引言：pair 类型

pair 类型，在 utility 头文件中定义

## 关联容器

关联容器不能通过容器大小来定义，因为这样的话就无法知道键所对应的值是什么了。

## map 类型

### map 对象的定义

```c++
map<string, int> word_count;	// empty map from string to int
```

map 构造函数：

```
map<k, v> m;
map<k, v> m(m2);
map<k, v> m(b, e);
```

>在实际应用中，键类型必须定义 < 操作符，而且该操作符应能“正确地工作”，这点很重要

### map 定义的类型

```
map<K, V>::key_type	
map<K, V>::mapped_type
map<K, V>::value_type
```

1. map 迭代器进行解引用将产生 pair 类型的对象

```c++
map<string, int>::iterator map_it = word_count.begin();

// *map_it is a reference to a pair<const string, int> object

cout << map_it->first
cout << " " << map_it->seconds;

map_it->first = "new Key";	// error:key is const
++map_it->second;	// ok cahnge value
```

pair 对象，first 成员存放键，为 const，second 存放值。

### 给 map 添加元素

- insert
- 先用下标操作符获取元素，通过给元素赋值


### 使用下标访问 map 对象

```c++
map<string, int> word_count;

word_count["Anna"] = 1;
```


2. 下标行为的编程意义

如果下标表示的键在容器中不存在，就会添加新元素。

```c++
map<string, int> word_count;
string word;
while(cin >> word){
	++word_count[word];
}
```

### map::insert 的使用



### 查找并读取 map 中的元素

```c++
map<string, int> word_count;
int occurs = word_count["foobar"]
```

上面这种方式，如果 `footbar` 键不存在就会创建新的元素返回 0。

```
m.count(k) 	返回 m 中 k 的出现次数
m.find(k)	如果 m 容器中存在 k 索引的元素，则返回指向该元素的迭代器。如果不存在就返回超出末端迭代器。
```

1. 检查 键 是否存在

```c++
int occurs = 0;
if(word_count.couint("foobar")){
	occurs = word.count["foobar"];
}
```

2. 读取元素而又不插入该元素

```c++
int occurs = 0;
map<string, int>::iterator it = word_count.find("foobar");
if(it != word_count.end()){
	occurs = it->second;
}
```

### 从 map 对象中删除元素

```
m.erase(k)
m.erase(p)
m.erase(b, e)
```

### map 对象的迭代遍历

```c++
map<string, int>::const_iterator map_it = word_count.begin();

while(map_it != word_count.end()){
	cout << map_it->first << " occurs "
		<< map_it->second << " times " << endl;
	++map_it;
}
```

### 单词转换 map 对象

## set 类型

### set 容器的定义和使用

```c++
set<string> set1;
set1.insert("the");

set1.insert(svec,begin, svec.end());

// query
iset.find(1)
iset.count(1)

set<int>::iterator set_it = iset.find(1);
*set_it = 11;
```

### 创建 单词排除 集

## multimap 和 multiset 类型

### 元素的添加和删除

```c++
// authors is multimap
authors.insert(make_pair(
	string("barth, John"),
	string("Sot-Weed Factor")
	));

authors.insert(make_pair(
	string("barth, John"),
	string("Sot-Weed Factor")
	));	


multimap<string, string>size_type cnt = authors.erase(search_item);
```

### 在 myltimap 和 multiset 中查找元素

## 容器的综合应用：文本查询程序

### 查询程序的设计

### TextQuery 类

### TextQuery 类的使用

### 编写成员函数

# 第十一章 泛型算法

标准库只提供了容器的基本操作。

排序，取最大值，取最小值等复杂操作需要，泛型算法

## 概述

1. 算法如何工作

每个泛型算法的实现都独立于单独的容器。

2. 标准算法固有地独立于类型

3. 迭代器将算法和容器绑定起来

## 初窥算法

```c++
#include<algorithm>
#include<numeric>
```

### 只读算法



### 写容器元素的算法

### 对容器元素重新排列的算法

## 在谈迭代器

### 插入迭代器

### iostream 迭代器

### 反向迭代器

### const 迭代器

### 五种迭代器

## 泛型算法结构

### 算法的形参模式

### 算法的命名规范

## 容器特有算法


# 第十二章 类

c++ 中，用类来定义自己的 抽象数据类型（abstract data type）。

三个新特征：

- 友元
- 可变成员
- 静态成员

## 类的定义和声明

### 类定义：扼要重述

### 数据抽象和封装

1. 访问标号实施抽象和封装


### 关于类定义的更多内容

1. 同一类型的多个数据成员

```c++
class Screen{
	public:

	private:
		std::string constents;
		std::string::size_type cursor;
		std::string::size_type height, width;
}
```

2. 使用类型别名来简化类


```c++
class Screen{
	public:
		typedef std::string::size_type index;

	private:
		std::string contents;
		index cursor;
		index hegiht, width;
}
```

3. 成员函数可被重载
4. 定义重载成员函数

```c++
class Screen{
	public:
		typedef std::string::size_type index;

		char get() const { return contens[cursor]};
		char get(index ht, index wd) const;

	private:
		std::string contents;
		index cursor;
		index height, width;
}
```


### 类声明和类定义

### 类对象

## 隐含的 this 指针

## 类的作用域

1. 使用类的成员

在类作用域之外，成员只能通过对象或指针分别使用成员访问操作符 `.` 或 `->` 来访问。

另一些直接通过类使用作用域操作符 `::` 来访问。


2. 作用域与成员定义

```c++
double Sales_item::avg_price() const{
	if(units_sold){
		return revenue/units_sold;
	}else{
		return 0;
	}
}
```

完全限定名 `Sales_item::avg_price` 来指出这是类 Sales_item 作用域中的 avg_price 成员的定义。


4. 函数返回类型不一定在类作用域中

```c++
class Screen{
	public:
		typedef std::string::size_type index;
		index get_cursor() const;
}

inline Screen::index Screen::get_cursor() const{
	return cursor;
}
```

函数的返回值类型必须使用 完全限定名。



## 构造函数

### 构造函数初始化

### 默认实参予构造函数

### 默认构造函数

### 隐式类类型转换

### 类成员的显式初始化

## 友元

## static 类成员

类静态成员

2. 定义 static 成员

```c++
class Account{
	public:
		void applyint(){
			amount += amount * interestRate;
		}
		static double rate(){
			return interestRate;
		}
		static void rate(double);

	private:
		std::string owner;
		double amount;
		static double interestRate;
		static double initRate();
};
```

3. 使用 static 成员

```c++
Account ac1;
Account *ac2 = &ac1;

double rate;
rate = ac1.rate();
rate = ac2->rate();
rate = Account:;rate();
```


### static 成员函数

Account 类中有 rate 的 static 成员函数，在类外部定义 static 成员时 无须重复指定 static 保留字：

```c++
void Account::rate(double newRate){
	interestRate = newRate;
}
```

### static 数据成员

# 第十三章 复制控制

复制构造函数：copy constructor, 是一种特殊的构造函数，具有单一形参，该参数时对该类类型的引用。

析构函数: destructor，当对象超出作用域或动态分配的对象被删除时，将自动应用析构函数。析构函数可用于释放对象时构造或在对象的生命期中所获取的资源。不管类是否定义了自己的析构函数，编译器都自动执行类中非 static 数据成员的析构函数。

## 复制构造函数

只有单个形参，而且该形参是对本类类型对象的引用（常用 const 修饰），这样的构造函数称为 复制构造函数。

作用：

- 根据另一个同类型的对象显示或隐式初始化一个对象
- 复制一个对象，将它作为实参传给一个函数
- 从函数返回时复制一个对象
- 初始化顺序容器中的元素
- 根据元素初始化式时列表初始化数组元素

1. 对象的定义形式

```c++
string null_book = "9-999-9999";	// copy-initialization

string dots(10, " .");	// direct-initialization
```


2. 形参与返回值

3. 初始化容器元素


### 合成的复制构造函数

### 定义自己的复制构造函数

### 禁止复制

## 赋值操作符

## 析构函数

## 消息处理示例

## 管理指针成员

### 定义智能指针类

### 定义值型类


# 第十四章 重载操作符与转换

442

# 第十五章 面向对象编程


## 面向对象编程：概述


面向对象编程的关键思想是 多态性

1. 继承
2. 动态绑定

## 定义基类和派生类


### 定义基类

```c++
class Item_base{
public:
	Item_base(const std::string &book = "",
			double sales_price = 0.0):
				isbn(book), price(sales_price){

				}

	std::string book() const {return isbn;}

	virtual double net_price(std::size_t n) const{
		return n * price;
	}


private:
	std::string isbn;

protected:
	double price;
};
```

1. 基类成员函数

`virtual` 保留字表示启用动态绑定

>基类将派生类需要重新定义的任意函数定义为虚函数

2. 访问控制和继承


### protected 成员

### 派生类

1. 定义派生类

```c++
class Bulk_item: public Item_base{

public:
	double net_price(std::size_t) const;

private:
	std::size_t min_qty;
	double discount;
};
```

2. 派生类和虚函数
3. 派生类对象包含基类对象作为子对象
4. 派生类中的函数可以使用基类的成员


### virtual 与其他成员函数

### 公用，私有和受保护的继承



### 友元关系与继承

基类或派生类可以使其他类或函数成为友元。

>友元关系不能继承

```c++
class Base{
	fridend class Frnd;

protected:
	int i;
};

class D1 : public Base{
protected:
	int j;
};

class Frnd{

public:
	int men(Base b){
		return b.i;	// ok
	}

	int men(D1 d){
		return d.i;	// error: friendship does not inherit
	}

}
```

### 继承与静态成员


## 转换与继承

### 派生类到基类的转换

如果有一个派生类型的对象，则可以使用它的地址对基类类型的指针进行赋值或初始化。

1. 引用转换不同于转换对象

2. 用派生类对象对基类对象进行初始化或赋值

3. 派生类到基类转换的可访问性

### 基类到派生类的转换



## 构造函数和复制控制

### 基类构造函数和复制控制

### 派生类构造函数

### 复制控制和继承

1. 定义派生类复制构造函数

```c++
class Base{ /* ... */}
class Derived: public Base{
public:
	Derived(const Derived& d):
		Base(d){
			//...
		}

}

```

### 虚析构函数

### 构造函数和析构函数中的虚函数

## 继承情况下的类作用域

每个类都保持着自己的作用域，在该作用域中定义了成员的名字。

在继承情况下，派生类的作用域嵌套在基类作用域中。

### 名字查找在编译时发生

### 名字冲突与继承

>与基类成员名相同的派生类成员将屏蔽对基类成员的直接访问。

### 作用域与成员函数

### 虚函数与作用域

```c++
class Base{
public:
	virtual int fcn();
};

class D1: public Base{
public:
	int fcn(int);
};

class D2: public D1{
public:
	int fcn(int);
	int fcn();
};
```

D1 中 fcn 没有重定义 Base 中的虚函数 fcn，相反它屏蔽了基类的 fcn。结果 D1 有两个名为 fcn 的函数。

D2 重定义了它继承的两个函数，它重定义了 Base 中定义的 fcn 的原始版本并重定义了 D1 中定义的非虚版本。

**通过基类调用被屏蔽的虚函数：**

```c++
Base bobj; D1 d1obj; D2 d2obj;
Base *bp1 = &bobj, *bp2 = &d1obj, *bp3 = &d2obj;

bp1->fcn();	// ok Base::fcn
bp2->fcn();	// ok Base::fcn
bp3->fcn();	// ok D2::fcn
```

## 纯虚函数

在函数形参表后面写上 =0 指定纯虚函数：

```c++
class Disc_item: public Item_base{
public:
	double net_price(std::size_t) const = 0;
};
```

将函数定义为存虚能够说明。该函数为后代类型提供了可以覆盖的接口，但是这个类中的版本绝不会调用。

用户不能创建 Disc_item 类型的对象。（类似 java 中的抽象类）


## 容器与继承

```c++
multiset<Item_base> basker;
Item_base base;
Buld_item bulk;

basket.insert(base);
basket.insert(bulk);	// ok, but bulk sliced down to its base part
```

## 句柄类与继承

c++ 中不能使用对象支持面向对象编程，必须使用指针或引用。

```c++
void get_prices(Item_base object, const Item_base *pointer, const Item_base &reference){

	// which version of net_price is called is determined at run time
	cout << pointer->net_price(1) << endl;
	cout << reference.net_price(1) << endl;

	// always invokes Item_base::net_price
	cout << object.net_price(1) << endl;
}
```

通过 pointer 和 reference 进行的调用在运行时根据它们所绑定对象的动态类型而确定。

使用指针或引用会加重类用户的负担。

定义包装（cover）类

句柄（handle）类

### 指针型句柄

### 复制未知类型

### 句柄的使用

## 再谈文本查询示例

### 面向对象的解决方案

### 值型句柄

### Query_base 类

### Query 句柄类

### 派生类

### eval 函数

# 第十六章 模版与泛型编程

## 模版定义

### 定义函数模版

```c++
template <typename T>
int compare(const T &v1, const T &v2){
	if(v1 < v2) return -1;
	if(v1 > v2) return 1;
	return 0;
}
```

### 定义类模版

```c++
template <class type> class Queue{
public:
	Queue();
	Type &front();
	const type &front() const;
	void push(const Type &);
	void pop();
	bool empty() const;
private:
	//...
}
```

1. 模版形参作用域
2. 使用模版形参名字的限制
3. 模版声明

### 模版类型形参

1. typename 与 class 的区别

在函数模版形参表中，关键字 typename 和 class 具有相同的含义。

```c++
template <typename T, class U> calc(const T&, const U&);
```

2. 在模版定义内部指定类型

### 编写泛型程序

## 实例化

1. 类的实例化

当编写 `Queue<int> qi`时，编译器自动chuangj `Queue<int>` 的类。

```c++
template <class Type> class Queue<int>{
public:
	Queue();
	int &front();
	const int &front() const;
	void push(const int &);
	void pop();
	bool empty() const;

private:
	//...
};
```

>类模版的每次实例化都会产生一个独立的类类型。

2. 类模版形参是必须的

```c++
Queue qs;	// error: which template instantiation?
```

3. 函数模版实例化

### 模版实参推断

1. 多个类型形参的实参必须完全匹配

```c++
template <typename T>
int compare(const T& v1, const T& v2){
	if(v1 < v2) return -1;
	if(v2 < v1) return 1;
	return 0;
}
```

上面模版在使用的时候 v1 和 v2 的类型必须一样。

2. 类型形参的实参的受限转换

v1 和 v2 之前只要可以转换到同一个类型也可以，比如（int short）可作为 (int, int) 处理

### 函数模版的显式实参

## 模版编译模型

## 类模版成员

### 类模版成员函数

### 非类型形参的模版实参

### 类模版中的友元声明

### Queue 和 QueueItem 的友元声明

### 成员模版

### 完整的 Queue 类

### 类模版的 static 成员

## 一个泛型句柄类

### 定义句柄类

### 使用句柄

## 模版特化

### 函数模版的特化

### 类模版的特化

### 特化成员而不特化类

### 类模版的部分特化

## 重载与函数模版

# 第十七章 用于大型程序的工具

## 异常处理




## 命名空间

### 命名空间的定义

### 嵌套命名空间

### 未命名的命名空间

### 命名空间成员的使用

### 类，命名空间和作用域

### 重载与命名空间

### 命名空间与模版


## 多重继承与虚继承

### 多重继承

### 转换与多个基类

### 多重继承派生类的复制控制

### 多重继承下的类作用域

### 虚继承

### 虚基类的声明

### 特殊的初始化语义


# 特殊工具与技术


















