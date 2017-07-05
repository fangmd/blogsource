---
title: Head First Python
date: 2016-08-27 13:18:12
tags: [Python, 读书笔记]
category: 读书笔记

---

# Head First Python 笔记

>结束 太基础了

# 初识 Python

查看电脑上Python的版本：

    python3 -V


<!--more-->

# 列表就像数组
在python中的list数据结构相当于java中的数组，数据项自下而上堆放，不过列表可以做很多数组做不到的操作，可以称为打了激素的数组。

python3 内置大概70多个BIF （build-in function）

# 文件与异常

    try...except...
    try...except TypeError[IOError]....

使用try处理异常而不是使用`if..else`增加逻辑复杂度

文件操作的几个函数：

    open()
    readling()
    seek()
    close()
# 持久存储

    out ＝ open('data.txt', 'w') #默认是r模式，写需要指定
使用标准输出，输出文件：将字符串写入到文件

    print('File content:', file=out)


处理文件模式:`try except finally`

使用`with`处理文件可以减少代码量，它不需要finally组来处理文件的关闭:

    try:
        with open('fiel.text', 'w') as data:
            print("sdf", file = data)
    except IOError as err:
        print('File error'+ str(err))

`with`语句使用一种上下文管理协议（context management protocol）的技术

## 对象序列化

pickle

    import pickle

    with open('file', 'wb') as mysavedata:
        pickle.dump([1, 2, 'sadf'], mysavedata)  # 保存列表到文件

    
# 推导数据

## 排序
1. 原地排序(In-place sorting):`sort()`

2. 复制排序(Copied sorting):`sorted()`

## list comprehension 列表推导
减少将一个列表转换为另一个列表所需要的时间

将一个列表－－》另一个列表需要做的：

1. 创建一个新的列表用来存放转换后的数据
2. 迭代处理的原列表中的各个数据项
3. 每次迭代时完成转换
4. 将转换后的数据存入新的列表

        clean_mikey = []
        for each_t in mikey:
            clean_mikey.append(sanitize(each_t))

    使用列表转换器：[最终值，迭代]

        clan_mikey = [sanitize(each_t) for each_t in mikey]

## set 删除list中的重复项 

    distances = set(james) # 参数是一个列表，工厂函数

工厂函数：用于创建某种类型的新数据项

# 定制数据对象 

## dict
与列表不同，字典不会维持插入的顺序，它重点时维护关联关系，而不是顺序。

## 类
将数据和代码打包

有助于降低复杂度，降低复杂度意味更少的bug，bug少意味着代码更可维护

使用`__init__()`定制对象的初始化

## self 的重要性
定义一个类，实际上是定义一个定制工厂函数

    a = Athlete()
 
Python内部处理上面的代码的过程：

    Athlete().__init__(a) # a就是对象的目标表示符
每一个方法的第一个参数都是self

类分为：定制类和继承类

# Web 开发
MVC设计Web应用

# 移动应用开发
。。。。
