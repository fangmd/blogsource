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

返回值可以是：值，引用，常量引用。

返回的对象会被复制到调用环境中。这一步是必须的，因为函数结束的时候临时变量所占用的空间就会被释放。

### 重载函数

## 异常

### 抛出异常

```c++
int abc(int a, int b, int c){
    if(a <= 0 || b <= 0 || c <= 0){
        throw "All parameters should be > 0";
    }
    return a + b * c;
}
```

### 处理异常

```c++
try
{
    
}
catch (exception e)
{

}
```

## 动态存储空间分配

### 操作符 new

操作符 new 用来进行动态存储分配或运行时存储分配，它的值是一个指针，指向所分配的空间。

```c++
int *y = new int(10);
```

### 一维数组

```c++
float *x = new float[n];
```

### 异常处理

### 操作符 delete

动态分配的存储空间不需要时应该把它释放.

```c++
delete y;
delete []x;
```

### 二唯数组

```c++
template <class T>
bool make2dArray(T ** &x, int numberOfRows, int numberOfColums){
    try{
        x = new T * [numberOfRows]

        for(int i=0; i < numberOfRows; i++){
            x[i] = new int[numberOfColums];            
        }
        return true;
    }catch(bad_alloc){
        return false;
    }
}
```

```c++
template <class T>

void delete2dArray(T ** &x, int numberOfRows){
    for(int i=0; i<numberOfRows; i++){
        delete []x[i];
    }
    delete []x;
    x = NULL;
}

```


## 自有数据类型

### 类 currency （货币类型）自定义类

currency 类声明

```c++
class currency{
    public:
        // 构造函数
        currency(signType theSign = plus,
                unsigned long theDollars = 0,
                unsigned int theCents = 0);

        // 析构函数
        ~currency(){}

        void setValue(signType, unsigned long, unsigned int);
        void setValue(double);
        signType getSign() const{ return sign; }
        unsigned long getDollars() const {return dollars;}
        unsigned int getCents() const {return cents;}
        currency add(const currency&) const;
        currency& increment(const currency&);
        void output() const;

    private:
        signType sign;
        unsigned long dollars;
        unsigned in cents;
}
```

```c++
void currency::setValue(signType theSign, unsigned long theDollars, unsigned int theCents){
    if(theCents > 99){
        throw illegalParameterValue("Cents should be < 100");
    }
    sign = theSign;
    dollars = theDollars;
    cents = theCents;
}

void currency::setValue(double theAmount){
    if(theAmount < 0){
        sign = minus;
        thisAmount = -theAmount;
    }else{
        sign = plus
    }
    dollars = (unsigned long) theAmount;
    cents = (unsigned int)((theAmount + 0.001 - dollars) * 100);
}
```

```c++
currency currency::add(const currency& x) const{
    long a1, a2, a3;
    currency result;

    a1 = dollars * 100 + cents;
    if(sign == minus) a1 = -a1;

    a2 = x.dollars * 100 + x.cents;
    if(x.sign == minus) a2 = -x.a1;

    a3 = a1 + a2;

    if(a3 < 0){
        result.sign = minus;
        a3 = -a3;
    }else{
        result.sign = plus;
    }

    result. dollars = a3 / 100;
    result.cents = a3 - result.dollars * 100;
    return result;
}
```

```c++
currency& currency::increment(const currency& x){
    *this = add(x);
    return *this;
}

void currency::output() const{
    if(sign == minus) cout << "-";
    cout << "$" << dollars << ".";
    if(cents < 10) cout << "0";
    cout << cents;
}
```

### 一种不同的描述方法

### 操作符重载

>定义类对象 +，-，*，/ 的运算方式

### 友元和保护性类成员

友元函数或方法可以反问私有成员。

### 增加 #ifndef #define #endif

## 异常类 illegalParameterValue

## 递归函数

递归函数：recursive function

直接递归：direct recursion

间接递归：indirect recursion

### 递归的数学函数

递归定义需要的条件：

- 有一个基础部分，它包含 n 的一个或者多个值，对这些值，f(n) 是直接定义的（即不用递归就能求解）
- 递归部分

典型例子：斐波那契数列(F0 = 0, F1 = 1, Fn = Fn-1 + Fn-2)

### 归纳

归纳证明

需要证明一个等式成立的时候，可以先确认 n=0 的时候等式成立。

再假设 n=n 时等式成立，在证明 n=n+1 时等式成立。

则等式成立

### C++ 递归函数

## 标准模版库

标准模版库（STL）：是一个容器，适配器，迭代器，函数度喜庆，算法集合。

## 测试与调试

### 什么是测试

### 测试数据的设计

1. 黑盒测试：I/O 分类和因果图。保证各种数据类型都测试到。
2. 白盒测试：基于代码来设计测试数据。对于一个测试集最起码的要求是程序的每条语句都至少执行一次。

### 调试

## 参考及推荐读物



# 第二章 程序性能分析

## 什么是程序性能

程序性能：运行这个程序所需要的内存和时间的多少。

使用两种方法确定程序的性能：

- 性能分析，分析方法
- 性能测量，实验方法

空间复杂度：程序运行时所需要的内存大小。

时间复杂度：程序运行所需要的时间。

## 空间复杂度

空间主要由下面几个部分组成：

1. 指令空间 instruction space: 编译之后程序指令所需要的存储空间
2. 数据空间 data space: 所有常量和变量值所需要的存储空间
3. 环境栈空间 environment stack space: 用来保存暂停的函数和方法在恢复运行时所需要的信息

### 空间复杂度的组成

### 距离

## 时间复杂度

### 时间复杂度的组成

### 操作计数

选择排序：

1. 首先找到最大的元素，把它和数组最后一个交换位置,在找到余下的最大值和倒数第二个位置的元素交换位置。
2. 或者找到最小值和第一个元素交换位置。。。

### 最好，最坏的平均操作计数

原地重排：

### 步数

一个程序步：可以大概地定义为一个语法或语义上的程序片段，该片段的执行时间独立于实例特征。

计算程序步的例子：

```c++
template<class T>

T sum(T a[], int i){
    T theSum = 0;
    stepCount++;

    for(int i = 0; i < n; i++){
        stepCount++;
        theSum += a[i];
        stepCount++;
    }
    stepCount++;    // for 最后的判断语句
    stepCount++;    // return 语句
    return theSum;
}
```

计算平均步数：所有情况步数和 / 情况种类

# 第三章 渐近记法






