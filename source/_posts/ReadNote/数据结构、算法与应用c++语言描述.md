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

<!--more-->

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

## 渐近记法

### 大 O 记法

```
1<logn<n<nlogn<n^2<n^3<2^n<n!
```

渐近记法 描述的是最大实例特征的时间和空间复杂度。

确定公式中的最大单项得出 大 O 记法：

```
3n^2 + 6nlogn + 7n + 5 = O(n2)

最大单项是 n2
```

### 渐近记法 的另两种表示方式

## 复杂度分析举例

## 实际复杂度

一个程序的复杂度如果是指数，它的实例特征 n 必须限制在适度小的范围内（<=40）.

一个函数的复杂度如果是高次多项式，它也必须限制使用。

# 第四章 性能测试

## 引言

性能测量（performance measurement） 关注的是一个程序实际需要的空间和时间。

## 选择实例的大小

n 的取值

1. 渐近分析仅对足够大的 n 给出了程序的复杂度。
2. 即使在渐近曲线的区间内，程序实际运行时间也可能不满足预定的渐近曲线，原因是渐近分析忽略了许多低次项的时间需求。

## 设计测试数据

## 实验设计

```c++
int main(){
    int a[1000], step = 10;

    double clocksPerMillis = double(CLOCKS_PER_SEC) / 1000;
    cout << "The worst-case time, int milliseconds, are" << endl;
    cout << "n \t Time" << endl;

    for(int n = 0; n <= 1000; n += step){
        for(int i = 0; i<n; i++){
            a[i] = n-1;
        }
        clock_t startTime = clock();
        insertionSort(a, n);
        double elapsedMillis = (clock() - startTime) / clocksPerMillis;
        cout << n << '\t' << elapseMillis << endl;

        if( n == 100) step = 100;
    }
    return 0;
}
```


如果 `insertionSort` 方法的执行消耗的时间很少的话，elapseMillis=0，精度不够。

做法：insertionSort 方法一直执行，直到  elapseMillis=1， 的时候跳出，同时记录 `insertionSort` 方法执行的次数，就能计算出单次时间了。

## 高速缓存

### 简单计算机模型



### 缓存未命中对运行时间的影响

# 第五章 线性表

数据的描述方式：数据在计算机内存和磁盘上的存储方式。

## 数据对象和数据结构

数据结构：是一个数据对象，同时这个对象的实例以及构成实例的元素都存在着联系，而且这些联系由相关的函数来规定。

研究数据结构，就是关心数据对象的描述以及相关函数的具体实现。

## 线性表数据结构

线性表(liner list) 也称 有序表(ordered list)

线性列表的操作：

1. 创建线性表
2. 撤销一个线性表
3. 确定线性表是否为空
4. 确定线性表的长度
5. 按一个给定的索引查找一个元素
6. 按一个给定的元素查找其索引
7. 按一个给定的索引删除一个元素
8. 按一个给定的索引插入一个元素
9. 从左至右顺序输出线性表元素

### 抽象数据类型 linearList

abstract data type， ADT

### 抽象类 linearList

存虚函数用数字 0 作为初始值。

```c++
template<class T>
class linearList{

public:
    virtual ~linearList(){};
    virtual bool empty() const = 0;
    virtual int size() const = 0;
    virtual T& get(int theIndex) const = 0;
    virtual int indexOf(const T& theElement) const = 0;
    virtual void erase(int theIndex) = 0;
    virtual void insert(int theIndex, const T& theElement) = 0;
    virtual void output(ostream& out) const = 0;
}
```

## 数组描述

### 描述

使用数组来存储线性表的元素。

### 变长一维数组

```c++
template<class T>
void changeLengthID(T*& a, int oldLength, int newLength){
    if(newLength < 0){
        throw illegalParameterValue("new length must be >= 0");
    }

    T* temp = new T[newLength];
    int number = min(oldLength, newLength);
    copy(a, a+number, temp);
    delete []a;
    a = temp;
}
```

### 类 arrayList

定义一个抽象类 linearList 的派生类 arrayList

arrayList 类定义：

```c++
template<class T>
class arrayList: public linearList<T>{

public:
    // 构造函数，复制函数，析构函数
    arrayList(int initialCapacity = 10);
    arrayList(const arrayList<T>&);
    ~arrayList() { delete []element;}

    // ADT function
    bool empty() const {return listSize = 0;}
    int size() const {return listSize;}
    T& get(int theIndex) const;
    int indexOf(const T& theElement) const;
    void erase(int theIndex);
    void insert(int theIndex, const T& theElement);
    void output(ostream& out) const;

    // other function
    int capacity() const{return arrayLength;}

protectd:
    void checkIndex(int theIndex) const;
    T* element;
    int arrayLength;
    int listSize;


}
```

arrayList 的构造函数和复制函数

```c++
template<class T>
arrayList<T>::arrayList(int initialCapacity){
    if(initialCapacity < 1){
        ostringstream s;
        s << "Initial capacity = " << initialCapacity << "Must be > 0";
        throw illegalParameterValue(s.str());
    }
    arrayLength = initialCapacity;
    element = new T[arrayLength];
    listSize = 0;
}

template<class T>
arrayList<T>::arrayList(const arrayList<T>& theList){
    arrayLength = theList.arrayLength;
    listSize = theList.listSize;
    element = new T[arrayLength];
    copy(theList.element, theList.element + listSize, element);
}
```

构造函数：O(1)
复制函数：O(n)

arrayList 基本方法

```c++
template<class T>
void arrayList<T>::checkIndex(int theIndex) const{
    if(theIndex < 0 || theIndex >= listSize){
        ostringstream s;
        s << "index = " << theIndex << " size ="<< listSize;
        thorw illegalIndex(s.str());
    }
}

template<class T>
T& arrayList<T>::get(int theIndex) const{
    checkIndex(theIndex);
    return element[theIndex];
}

template<class T>
int arrayList<T>::indexOf(const T& theElement) const{
    int theIndex = (int) (find(element, element + listSize, theElement) - element);

    if(theIndex == listSize){
        // not find
        return -1;
    }else{
        return theIndex;
    }
}
```

删除一个元素

```c++
template<class T>
void arrayList<T>::erase(int theIndex){
    checkIndex(theIndex);

    copy(element + theIndex + 1, element + listSize, element + theIndex);
    element[--listSize].~T();   // 调用析构函数
}
```

利用 copy 算法把索引从 theIndex+1, theIndex+2,..., listSize-1 的元素左移一个位置，然后 listSize 值减1。

插入一个元素

```c++
template<class T>
void arrayList<T>::insert(int theIndex, const T& theElement){
    if(theIndex < 0 || theIndex > listSize){
        ostringstream s;
        s << "index = " << theIndex << " size = " << listSize;
        throw illegalIndex(s.str());
    }

    if(listSize == arrayLength){
        // 数组已经满了，增加数组长度
        changeLengthID(element, arrayLength, 2 * arrayLength);
        arrayLength *= 2;
    }

    // 元素右移一位
    copy_backward(element + theIndex, element + listSize, element + listSize +1);
    element[theIndex] = theElement;
    listSize++;
}
```

输出函数output和重载<<

```c++
template<class T>
void arrayList::output(cout->out) const{
    copy(element, element + listSize, ostream_iterator<T>(cout, " "));
}

template<class T>
ostream& operator<<(ostream& out, const arrayList<T>& x){
    x.output(out);
    return out;
}
```

减少数组长度

可以修改 erase 方法，当 `listSize<arrayLength/4` 时减少数组长度。

### C++ 迭代器

一个迭代器 iterator 是一个指针，指向对象的一个元素。

一个迭代器可以用来逐个访问对象的所有元素。


数组迭代器：

y 初始化指向 x[] 的首元素（x 实际上就是指向数组首元素的指针）

y++: 表示指向数组的下一个元素

x+3: 是一个指针

*y: 指针 y 的解引用 获取 y 指向的值。

```c++

int main(){
    int x[3] = {0, 1, 2};

    // 用指针 y 遍历数组
    for(int* y=x; y!=x+3; y++)
        cout << *y << " ";

    return 0;
}
```

抽象化的迭代器代码:

```
for(iterator i=start; i!=end; i++){
    cout << *i << " ";
}

输出范围：[start, end)
start: 指向范围的首元素
end: 指向要输出的最有一个元素的下一个位置
```

### arrayList 的一个迭代器


arrayList 类中增加：

1. 迭代器成员对象
2. begin() 方法：返回指向第一个元素的 迭代器
3. end() 方法：返回指向最后一个元素的下个位置的 迭代器

```
class iterator
iterator begin(){return iterator(element);}
iterator end(){return iterator(element+listSize);}
```


iterator.class:

```c++
class iterator{

public:
    typedef bidirectional_iterator_tag iterator_category;
    typedef T value_type;
    typedef ptrdiff_t difference_type;
    typedef T* pointer;
    typedef T& reference;

    // constructor
    iterator(T* thePosition=0){positino = thePosition;}

    //解引用操作符
    T& operator*() const {return *position;}
    T* operator->() const {return &*position;}

    // 迭代器值增加
    iterator& operator++(){ // 前加
        ++position;
        return *this;
    }
    iterator operator++(int){ // 后加
        iterator old = *this;
        ++position;
        return old;
    }

    iterator& operator--(){
        --position;
        return *this;
    }
    iterator operator--(int){
        iterator old = *this;
        --position;
        return old;
    }

    // 测试是否相等
    bool operator!=(const iterator right) const{
        return position != right.position;
    }
    bool operator==(const iterator right)const{
        return position == right.position;
    }

protected:
    T* position;
}
```

## vector 描述

STL 中提供了基于数组的类 vector. 这个类中有 arrayList 的所有功能。

# 第六章 线性表--链式描述

在链式描述中，线性表的元素在内存中的存储位置是随机的。每个元素都有明确的指针或链指向线性表的下一个元素的位置。

在数组描述中，元素的地址可以通过数学公式决定和获取。但是在链式描述中，元素地址是随机分配的。

几个概念：

- 链式描述
- 链表，循环表，双向链表
- 头节点

STL 的容器类 list 使用带头节点的双向循环链表描述实例。

## 单向链表

### 描述











