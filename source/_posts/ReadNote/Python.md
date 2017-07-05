---
title: Python 学习记录
date: 2016-08-14 21:38:14
tags: [Python, 廖雪峰笔记, 简明Python]
categories: Python

---

# 第一个Python程序

1. 编写一个文件：`hello.py`

		print('hello , world');
2. 运行这个文件

		python hello.py
3. 运行文件方法二：

    	// 1. 修改文件内容
	    #!/usr/bin/env python3
	    print('hello,world')

	    // 2. 给文件添加执行权限
	    chmod a+x hello.py

	    // 3. 执行文件
	    ./hello.py

上面使用了交互模式和直接运行`.py`文件

<!--more-->

## Python 脚本运行 传入参数

	# ex14.py
	from sys import argv

	script num1 num2 = argv # 输入的参数进行解包

使用：

	python3 ex14.py num1 num2

python3 后面的参数会被打包传入py脚本，通过argv解包获取参数。

## Python 获取控制台的输入

	# ex13.py
	a = input()

	prompt = '>' # 输入提示内容
	a = input()

# Python基础

## 操作符

- `+`
- `-`
- `*`
- `/`:返回浮点数
- `//`:整数商
- `%`:取余数
- `**`:次方，幂（3 ** 4 = 81）
- `=`:assign a value to a variable
- '\_':mines last printed 
- `>>`:右位移
- `<<`:左位移
- `&`
- `|`
- `^`
- `~`
- `<`
- `<=`
- `>`
- `>=`
- `!=`
- `and`
- `not`
- `or`

## 基本数据

### numbers 数字
Python 只有 3 种数字类型： 整数（比如 2），浮点（比如 2.3），复数（比如 -5+4j）


### Strings
python3 中的所有字符串都是 unicode


使用单眼号或者双眼号

三眼号：字符串内部换行其他写法：`"""..."""` , `'''...'''`

```
    print('''line1
    line2
    line3''')

    加一个`\`可以去除开头生成的多余的新行
```

`\\`转义符号 ,`\\n`表示换行，`\\t`表示制表符

外部使用单眼号，内部使用双眼号的时候，双眼号不用转义

使用`r`(raw)表示眼号内需要转义的字符都默认不转义:

    print(r'C:\some\name') # C:\some\name

#### format 

```python
#! /usr/bin/env python3
age = 25
name = 'fang'
print('{0} is {1} years old'.format(name, age))
```

##### format 是如何工作的
string 可以包含**格式说明符**，format 使用参数替换 string 中的**格式说明符**

还有一些复杂的**格式说明符**使用方式


#### String concatenate

- `+`:concatenate string
- `*`:repeat string

		3 * 'un' + 'ium' # 'unununium'

- 两个string并排自动 concatenate, 只对于字符串对于变量或者表达式无效

		'Py' 'thon' # 'Python'
	如果要将字符串和变量拼接需要使用`+`

#### string index
可以使用负索引

越界会报错：`IndexError`

#### 截取 slicing
	s[x:y]

	word = 'Python'
	word[0:2]
	word[:2] + word[2:] # 'Python'

	+---+---+---+---+---+---+
	| P | y | t | h | o | n |
	+---+---+---+---+---+---+
	0 	1   2   3   4   5   6
	-6  -5  -4  -3  -2  -1

索引越界不会报错：

	word[4:42] # 'on'
	word[42:] #''

字符串属于常量，不能使用索引赋值，只能创建新的string：

	'J' + word[1:] # Jython
	word[:2] + 'py' # python

#### built-in function
相关方法见项目

format:

	'The value {0}, {1}, {0}'.format(1, 2)

	result = "The square root of 5 is {0:.{1}f}".format(math.sqrt(5), 4)


#### lean from koans

1. 字符串中换行站一个字符
2. `str == "navel".__class__` # True
3. 97 == ord('a')

用正则分割 string：

	string = "the,rain;in,spain"
	pattern = re.compile(',|;')
	words = pattern.split(string) # list

字母大小写处理：

	self.assertEqual('Guido', 'guido'.capitalize())
	self.assertEqual('GUIDO', 'guido'.upper())
	self.assertEqual('timbot', 'TimBot'.lower())
	self.assertEqual('Guido Van Rossum', 'guido van rossum'.title())
	self.assertEqual('tOtAlLy AwEsOmE', 'ToTaLlY aWeSoMe'.swapcase())

快速拼接list：

	words = ["Now", "is", "the", "time"]
	self.assertEqual('Now is the time', ' '.join(words))


### boolean
`True`

`False`

运算：`and`,`or`,`not`


### None

	None is None # True  There is only one None

	isinstance(None, object) # True

### 空值
`None`

### 变量

    a = 'ANC`

上面变量的赋值Python解释器做了两件事：

1. 在内存中创建一个`ABC`字符串
2. 在内存中创建一个名为`a`的变量，并把它指向`ABC`

### 常量

使用大写的变量表示常量：

    PI = 3.14159

实际上PI还是一个变量，Python中没有任何机制来保证他不回改变，这只是开发者定义的使用方法。

除法：

1. `/`：结果是精确的浮点
2. `//`:地板除，结果还是整数

Python的整数和浮点数都没有大小限制，但是超出一定范围就直接表示为`inf`（无限大）

## 字符串和编码

### 字符编码

Unicode把所有语言都统一到一套编码里

`UTF-8`编码：可变长的Unicode编码，节省空间

### Python 的字符串
在最新的Python3版本中，字符串是以Unicode编码的，也就是说Python的字符串支持多语言。

- `ord()`:获取字符的整数表示
- `chr()`:把编码转换成对应的字符
- `encode()`:方法把字符串编码成指定的`bytes`
- `deocde()`:把获取到的字节流转换成字符串
- `len()`:获取字符串的长度，或者字节的字节数（由调用对象决定）

为了防止乱码，可以在`py`文件中添加：

    # -*- coding: utf-8 -*-

告诉解释器按照UTF－8编码读取源代码

### 格式化字符串
python中格式化字符串的方式和C语言一致使用`%`实现：

    `hello, %s' % 'wordl'
    `Hi, %s, you have $%d.' % ('Michael',1000)

占位符：

1. `%d`
2. `%f`
3. `%s`
4. `%x`

        >>> '%2d-%02d' % (3, 1)
        '  3-01'
        >>> '%.2f' % 3.1415926
        '3.14'

注意：`%s`可是适配任何数据类型，`%%`来做转义表示一个％

# 数据结构

## list 列表
Python内置数据类型列表：list，是一种有序集合

    classmates = ['python', 'java', 'scheme']

- `len(列表对象)`:获取列表长度，参数为列表对象
- 使用索引访问列表中的元素（和数组一样）, 也有切片操作，支持用`+`号连接
- 反问最后一个元素快捷方法：`classmates[-1]`使用－1作为索引，－2表示倒数第二个，以此类推
- 添加元素：`classmates.append('Adam')`,`classmates.insert(1,'Jack')`
- delete element:`classmates.pop()`,`classmates.pop(1)`
- replace element:`classmates[1] = 'Sarah'`
- 列表中存储的数据类型可以是多种
- 二维数组实现方式：数组里面的元素也是数组

### 利用切片增 删 改
	lettters = ['a', 'b', 'c', 'd']
	letters[2:5]=['C', 'D', 'E']
	letters[2:5]=[] # remove
	letters[:] = [] # clear

### 配合 range 生成 list

	self.assertEqual([5, 4], list(range(5, 3, -1)))
	self.assertEqual([0, 2, 4, 6], list(range(0, 8, 2
	self.assertEqual([1, 4, 7], list(range(1, 8, 3)))
	self.assertEqual([5, 1, -3], list(range(5, -7, -4)))
	self.assertEqual([5, 1, -3, -7], list(range(5, -8, -4)))

### list 一些方法
- `list.append(x)`
- `list.extend(L)`: Extend the list by appending all the items in the given list.
- `list.insert(i, x)`
- `list.remove(x)`
- `list.pop([i])`:The square brackets around the i in the method signature denote that the parameter is optional, 中括号表示参数是可选的
- `list.clear()`
- `list.index(x)`
- `list.count(x)`
- `list.sort(key=None, reverse=False)`
- `list.reverse()`
- `list.copy()`

### list assignments

	first_name, last_name = ["John", "Smith"]

	title, *first_names, last_name = ["Sir", "Ricky", "Bobby", "Worthington"] # first_names: "Ricky", "Bobby"

### 交换两个数

	first_name, last_name = last_name, first_name


### Using Lists as Stacks 堆栈

	list.append(5)
	list.pop()
### Using Lists as Queues 队列

	list.append(2)
	list.popleft()

### List Comprehensions 列表解析
A list comprehension consists of brackets containing an expression followed by a for clause, then zero or more for or if clauses.

一个列表解析器由方括号和`for`，`if`表达式组成。

对集合中每个元素进行操作：

	squares = list(map(lambda x: x**2, range(10)))
	# or better
	squares = [x**2 for x in range(10)]

列表解析器也可以使用复杂表达式和方法函数：

	[str(round(p1, i)) for i in range(1, 6)]

### Nested List Comprehensions
嵌套列表解析器

	[[row[i] for row in matrix] for i in range(4)]

### The del statement 删除语句
使用索引删除集合中的元素

	a = [-1, 23, 34, 45]
	del a[0]
	del a[2:3]
	del a[:]
	del a

## tuple
元组，一旦初始化就不能修改(immutable)

创建方式：

    classmates = ('Michael', 'Bob', 'Tracy')
	# or
	classmates = 1234, 2133213, '23432'

因为不可变所以更加安全，在代码优先使用 tuple

定义一个元素的 tuple ,需要加逗号，来消除歧义

    t = (1, )
	(1).__class__ # int

没有 append

	with self.assertRaises(AttributeError): count_of_three.append("boom")

tuple -> list:

	a = [1, 2]
	a = list(a)
	a.append(3)
	a = tuple(a)

str -> tuple:

	tuple('Hello') # ('H', 'e', 'l', 'l', '0')

## unpack list tuple

	a, b = [1, 2]
	first, *middle, last = [1, 2, 3, 4, 5 ]

## set
和dirt类似不过是key的集合，但是不存储value

基本用途：成员测试和消除重复条目，集合对象还支持并集、 交集、 差和对称差等数学运算。

创建：需要一个list作为输入集合

	s = set([1,2,3])
	# or
	basket = {'apple', 'orange', 'apple', 'pear', 'orange', 'banana'}


由于存储的是key，不可重复,无序

添加：`add(key)`
删除：`remove(key)`


可以作交集和并集的操作：

	s1 - s2 # letters in a but not in b
	s1 & s2 # 交集
	s1 | s2 # 并集
	a ^ b # letters in a or b but not both

### set Comprehensions
		a = {x for x in 'asdfwer' if x not in 'abc'}

### & | ^ - operate


	scotsmen = {'MacLeod', 'Wallace', 'Willie'}
    warriors = {'MacLeod', 'Wallace', 'Leonidas'}

    self.assertEqual({'Willie'}, scotsmen - warriors)
    self.assertEqual({'MacLeod', 'Wallace', 'Willie', 'Leonidas'}, scotsmen | warriors)
    self.assertEqual({'MacLeod', 'Wallace'}, scotsmen & warriors)
	self.assertEqual({'Willie', 'Leonidas'}, scotsmen ^ warriors)

### other

	{} # dict
	self.assertEqual(True, 'cow' not in set('apocalypse now') )
	self.assertEqual(True, set('cake').issubset(set('cherry cake')) )



### dict
字典：dictionary, map

创建一个dict

    e = dict()
	d = {'Michael':98, 'Bob':76, 'Tracy':98}

使用：

	d['Michael']

无论这个字典由多大，他的查询速度都不会变慢，因为dict内部可以直接计算出值的存放位置

获取 keys，获取 values：

	d.keys()
	d.values()

批量插入 keys： values=42

	result = {}.fromkeys(('red warrior', 'green elf', 'blue valkyrie', 'yellow dwarf', 'confused looking zebra'), 42)

判断key是否存在：

1. 通过`in`

	`Michael` in d # True:表示存在 ，False 表示不存在

2. 通过`get()`方法，不存在返回None，或者自己指定的Value

		d.get('Thomas', -1)
		d.get('Thomas')

注意返回`None`的时候，交互式命令行不会显示结果

删除key：

		d.pop('Bob') # 返回被删除的值

和list的区别：

	1. 查找和插入的速度快
	2. 需要占用大量的内存

用空间换取速度

通过key计算位置的算法：哈希算法


3. loop

		for k, v in a_dict.items():
			pass

#### dict sort
通过 zip 将 dict --》zip, max 将 zip --》 list

	a = {
	    'a': 12,
	    'b': 34,
	    'v': 1,
	    'f': 123,
	}

	print(max(zip(a.values(), a.keys())))
	print(min(zip(a.values(), a.keys())))
	print(sorted(zip(a.values(), a.keys())))

## 条件判断 More Control Flow Tools

- if
- else
- elif

注意要加冒号，Python依靠缩进规则会执行判断语句后面的缩进代码

if判断条件简写：

    if x:
        print('True')

只要x是非零数值，非空字符串，非空list等，就判断为True，否则False

比较例子：

	23 == 2
	23 is 2
	'nam' is 'name'
	n in nums

>python没有switch语句，你可以使用if…elif…else语句达到同样的目的(有时用字典代替会更加快捷)。

## input

    s = input('birth:')
    birth = int(s)
    if brith < 2000:
        print('00 pre')
    else:
        priint('00 after')

注意：input返回的数据类型是`str`


## zip

	first = ['a', 'b', 'c']
	last = ['4', '5', '6']
	names = zip(first, last) # [('a', '4'), ('b', '5'), ('c', '6')]
	for a, b in names
	print(a, b)


## 循环

### 循环

1. `for x in xs`,把每个xs中的每个元素代入变量x，然后执行缩进快的语句
2. `while`,`if`

### range

使用`range()`生成整数数列：

    sum = 0
    for x in range(101):
        sum = sum + x
    print(sum)

	range(3, 5)

	range(0, 101, 4) # 0-100每隔三个取一个数 0 4 8

### break

跳出循环

### continue

跳出本次循环，继续下次循环


# Modules
可共享的代码

Python 支持在一个文件中编写代码然后在运行的时候被其他程序块调用，这样叫做模块。

模块可以被导入到其他模块中使用。

一个Python文件就是一个模块

## More on Modules
模块中可以包含一段运行代码在模块被引用的时候执行，并只执行一次用作初始化Module。

import 的两种方式：

    from fibo import fib, fib2
    from fibo import *


### Executing modules as scripts
运行一个Python moule:

    python3 fibo.py <arguments>

### The Module Search Path
脚本运行的时候会优先去内部模块中查找导入的模块，然后在脚本所在目录中查找

模块注释：一个三重引号引起的字符串没有赋给一个变量就会被认为是注释

    """ 。。。。
    。。。。
    。。。。"""


## Packages
包管理模块命名空间。

## 给模块创建发布

1. 创建一个文件夹，将模块放入
2. 创建一个文件`setup.py`
    这个文件包含有关发布的元数据

        from distutils.core import setup
        setup(
            name    ='nester',
            version ='1.0.0',
            py_modules  =['nester'],
            author  ='double'
            author_email    ='sdf@sdf'
            url = 'http://asd.com'
            description ='A simple'
        )
3. 在上面创建的文件目录下：
        python3 setup.py sdist
4. 将发布安装到Python本地中：
        sudo python3 setup.py install


# 函数

可重用的代码

## 调用函数

    max(2, 3)

### 数据类型转换

    int(...)
    float(...)
    str(...)
    bool(...)

函数名：创建一个函数对象的引用

    a = abs # 变量a指向abs函数
    a(-1) # 调用

## 定义函数

`def`语句，函数名，括号，参数，冒号：

    def my_abs(x):
        if x>=0:
            return x
        else:
            return -x

### 空函数

    def nop():
        pass

`pass`可以作为占位符，先让代码运行起来，后续再编写，也可以用在if语句块中

    if age >= 18:
        pass # 如果没有pass代码就会报错

### 参数检查

定义：

    def my_abs(x):
        if not isinstance(x, (int, float)):
            raise TypeError('bad operand type')
        if x >= 0:
            return x
        else:
            return -x

### 返回多个值
比如返回一个坐标：

    import math

    def move(x, y, step, angle=0):
        nx = x + step * math.cos(angle)
        my = y - step * math.sin(angle)
        return nx, ny
    x, y = move(100, 100, 60, math.pi/6)

这里其实函数返回的值是tuple

## 函数的参数

### 位置参数
正常的函数参数

### 默认参数

    def power(x, n=2):
        s = 1
        while n>0:
            n = n-1
            s = s*x
        return s

上面的n表示默认参数，在函数调用的时候可以不指定，只有在调用和默认参数不符合的时候才需要指定第二个参数

注意：默认参数必须指向不变对象,默认参数的位置必须在非默认参数的后面。

如果要将默认参数放在前面： keyword-only
```python
def print_sum(a=1, *, b, c):
    print(a,b,c)

print_sum(2, b=3, c=3) # result: 2 3 3
```

### 可变参数 Arbitrary Arguments Lists
传入集合不方便：

    def calc(numbers):
        sum = 0
        for n in numbers:
            sum = sum + n * n
        return sum

定义可变参数：

    def calc(*numbers):
    	....

将list或者tuple变成可变参数传入：

    nums = [1,2,3]
    calc(*nums)


### 关键字参数 Keyword Arguments
一个键值对

    def person(name, age, **kw):
        print('name', name, 'age:',age, 'other:', kw)

关键字参数在函数内部会自动组装成一个dict

### 命名关键字参数
检查传入的关键字参数：

    def person(name, age, **kw):
        if 'city' in kw:
            pass
        if 'job' in kw:
            pass
        print('name:', name,'age:',age, 'other:',kw)

使用函数：

    person('jack', 24, city='Beijing', addr='Chaoyang')

限制关键字参数的名字：

    def person(name, age, *, city, job):
        pass

如果函数中已经定义了可变参数，后面的命名关键字参数就不需要特殊分隔符了：

    def persion(name, age, *args, city, job):
        pass

### unpacking arguments

e.g

	nums = [13, 23, 24]

	def sums(x, y, z):
		print(x+y+z)

	sums(*nums)

### 参数组合
在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。

顺序：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

## return 语句

一个不带有返回值的return语句相当于返回return None。

每个函数的末尾都隐含的包含一个return None语句除非你编写了自己的return语句。

## 递归函数

    def fact(n):
        if n==1:
            return 1
        return n*fact(n-1)

使用递归需要注意防止栈溢出，因为在计算机中，函数调用通过栈（stack）这种数据结构实现的，每一层调用栈内就加一层栈帧，递归由多少层就有多少层栈。

### Lambda Expressions
方法中只有一个表达式的时候使用：s表示方法参数，s+2 表示结果

	s = lambda s: s+2

### 方法文档 DocStrings

	def my_function():
		""" Do nothing, but document it.

		No, really, it does not do anything.
		"""
		pass
	print(my_function.__doc__)


# 高级特征

## 切片 slice

取出list或者tuple的部分元素

    L[0:3]

开始索引和结束索引，如果开始为0可以省略，同样范围是包头不包尾

支持倒数：索引为负数，倒数第一个元素是－1

- 取前10: `L[:10]`
- 取后10: `L[-10:]`
- 前11-20: `L[10:20]`
- 前10个数，每两个取一个: `L[:10:2]`
- 所有的数，每5个取1个: `L[::5]`
- 复制一个list: `L[:]`
- 复制一个倒序list: `L[::-1]`

字符串也可以使用切片操作：

    'ABCDSS'[:3]

## 迭代 iteration

`for...in`

迭代dict时，默认时迭代key，如果要迭代value：`for value in d.values()`,同时迭代：`for k,v in d.items()`

字符串也是可迭代对象

判断一个对象是否可迭代：

    form collections import Iterable
    isinstance('acb', Iterable) # str是否可迭代 ， True

如果要实现下标循环，可以使用`enumerate`函数把一个list变成索引－元素对：

    for i,value in enumerate(['A','B','C']):
        print(i, value)

## 列表生成式 List Comprehensions

生成一个整数列表：`range(x,y)` , x，y表示生成的范围，包头不包尾

生成x＊x的列表：`[x * x for x in range(1, 11)]`

还可以加判断：`[x * x for x in range(1, 11) if x % 2 == 0]` # [4, 16, 36, 64, 100]

两层循环生成全列表：`[m + n for m in 'ABC' for n in 'XYZ']`

列出单前目录下的全部文件和目录名：

    import os
    [d for d in os.listdir('.')] # os.listdir 可以列出文件和目录

使用两个变量来生成列表：`[k + '+' + v for k, n in d.items()]` # d是一个dict

把list中的所有字符串变成小写：`[s.lower for s in L]` # L is a list

## 生成器

通过列表生成式，我们可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。

所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。

### 创建generator:

方法一：将`[]`-->`()`

    L = [x * x for x in range(10)]
    g = (x * x for x in range(10))

如何打印生成器：`next(g)`,调用这个方法计算下一个元素的值，直到退后一个元素，没有更多元素的时候抛出`StopIteration`的错误 ； 使用 `for n in g`

还可以使用函数来生成，比如：

    def fib(max):
        n, a, b = 0, 0, 1
        while n < max:
            yield b
            a, b = b, a + b
            n = n + 1
        return 'done'

如果函数中包含了关键字`yield`这个函数就成为了generator

    f = fib(6)

## 迭代器

已经知道可用`for`的有

1.  `list`,`tuple`,`dict`,`set`,`str`
2. `generator`,包括生成器和带`yield`的generator function

可以直接作用于`for`循环的对象统称为可迭代对象：`Iterable`

可以使用`isInstance()`来判断是否属于某个对象

迭代器都是`Iterator`对象，但是`List`,`dict`,`str`虽然是`Iterable`,但不是`Iterator`

将`Iterable`转化成`Iterator`: `iter()`

    isinstance(iter('abc', Iterator))

Python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。

Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。



# 函数式编程
Functional Programming

特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

## 高阶函数 Higher-order funciton

### 变量可以指向函数

    f = abs

### 函数名也是变量

    abs ＝ 10   
    abs(-10) # 报错


### 传入函数
应为变量可以指向一个函数，所以一个指向函数的变量也可以作为参数传入一个函数，就成为了一个高阶函数

    def add(x, y, f):
        return f(x) + f(y)

高阶函数：将函数作为参数

### map/reduce

`map()`,`reduce()`

#### map()
接收两个参数：1. 函数 2. Iterable

返回结果：Iterator,惰性序列

    def f(x):
        return x * x

    r = map(f, [1, 2, 3, 4])
    list(r) # [1, 2, 9, 16]

map返回的是惰性序列，因此需要通过`list()`函数把整个序列都计算出来返回一个list

#### reduce()
接收两个参数： 1. 函数 2. Iterable

将结果继续和序列的下一个元素作累积计算：求和

    from functools import reduce
    def add(x,y ):
        return x + y
    reduce(add, [1, 2, 3, 4])

将序列[1, 2, 3]--> 123:

    from functools import reduce
    def fn(x, y):
        return x*10+y
    reduce(fn,[1, 2, 3])

### filter()
用于过滤序列

将函数作用于每个元素，返回`True`表示删除,`False`留下

返回的结果也是`Iterator`

#### 用fliter求素数

### sorted

#### 排序算法

Python内置`sorted()`

    sorted([23,45,76])

是一个高阶函数，还可以接收一个key函数来自定义排序方式

比如按绝对值大小排序：

    sorted([23,23,23,], key=abs)

反向排序：

    sorted(['wer','sdfdf','sdfe','df'], key=str.lower, reverse=True)

## 返回函数

### 函数作为返回值

高阶函数：除了可以将函数作为参数，同时也可以将函数作为返回值

### 闭包 Closure
闭包函数没有立即执行，返回函数不要引用任何循环变量，或者后续会发生变化的量。

如果要引用循环变量：

    def count():
        def f(j):
            def g():
                return j*j
            return g
        fs = []
        for i in range(1, 4):
            fs.append(f(i)) # f(i) 立即被执行，因此i的当前值被传入f()
        return fs

## 匿名函数

匿名函数有个限制：就是只能有一个表达式，不同写`return`，返回值就是表达式的结果

好处： 1. 不用写函数名，不同担心函数名冲突 2. 匿名函数也可以被变量指向

    f = lambda x: x * x


## 装饰器

函数对象有一个`_name_`属性，可以拿到函数的名字：

    def now():
        print('2016-8-14')
    f = now
    now._name_ # 'name'
    f._name_ # 'now'

使用装饰器扩张函数的功能

    def log(func):
        def wrapper(*args, **kw):
            print('call %s():', % func._name_)
            return func(*args, **kw)
        return wrapper

    @log
    def now():
        print('2016-8-14')

调用now不回执行函数本身

由于log()是一个decorator，返回一个函数，所以，原来的now()函数仍然存在，只是现在同名的now变量指向了新的函数，于是调用now()将执行新函数，即在log()函数中返回的wrapper()函数。

wrapper()函数的参数定义是(*args, **kw)，因此，wrapper()函数可以接受任意参数的调用。在wrapper()函数内，首先打印日志，再紧接着调用原始函数。


## 偏函数 Partial function
Python的`functools`模块提供了很多有用的功能

n进制转换

    int('1234', base=8)

    def int8(x, base=2):
        return int(x,base)

创建偏函数：

    import functools
    int8 = functools.partial(int, base=8)

偏函数的作用就是把一个函数的参数给固定住，也就是设置默认值，返回一个新的函数，方便使用。

也可以接收函数对象，`*args`,`**kw`

**kw:

    kw = {'base':2}
    int('10010', **kw)

*args:

    max2 = functools.partial(max, 10) # 实际上是把10加到了max参数中
    max2(4,5,3) # 结果是 10

# 模块
一个`.py`文件可以称为一个模块（Module）

好处：

1. 提高代码的可维护性
2. 复用性好

为了避免函数名相同，Python中有包来阻止模块

每个包的目录下都会有一个`_init_.py`文件，

## 使用模块

### 作用域

private：`_xxx`,用于代码封装

## 安装第三方模块
Python中安装第三方包是通过pip包管理工具完成的

# 面向对象编程 oop

定义一个对象：

    class Student(object):
        def __init__(self, name, score):
            self.name = name
            self.score = score

        def print_score(self):
            print('%s: %s' % (self.name, self.score))

给对象发送消息实际上是调用对象的方法：

    lisa = Student('Lisa Simpson', 87)
    lisa.print_score()

三大特征：封装，继承，多态

## 类和实例

    class Student(object):
        pass

类关键字，类名，参数为其父类

构造方法：

    def __init__(self, name, score):
        self.name = name
        self.score = score

注意这里的第一个参数永远是`self`

使用构造方法：

    bart = Student('Bart Simpson', 87)


### 数据封装

    def print_score(self):
        print('%s: %s' % (self.name, self.score))

    bart.print_score()


## 访问限制

让内部属性不被外部访问，加`_`即可；

    class Student(object):

        def __init__(self, name, score):
            self.__name = name
            self.__score = score

        def print_score(self):
            print('%s: %s' % (self.__name, self.__score))

        def get_name(self):
            return self.__name
        def get_score(self):
            return self._score
        def set_score(self, score):
            if 0<= score <= 100:
                self.__score = score
            else:
                raise ValueEoor('bad score')


访问双下划线的实例变量：其实Python只是更改了属性名

    bart._student__name ＃ 但是不同的Python解释器可能会把__name改成不同的变量名


## 继承和多态

    class Dog(Animal):
        def eat(self):
            print('Dog eatting....')


## 获取对象信息

### 使用 type()

    type(123) # <class 'int'>
    type(abs) # <class 'builtin_function_or_method'>

返回的是Class类型：

    type(13) == int # True

判断一个对象是否是函数：

    import types
    def fn():
        pass

    type(fn) == types.FunctionType # True
    type(lambda x: x)==types.LambdaType
    types.BuiltinFunctionType
    types.GeneratorType

### 使用isinstance()

    isinstance(a, b)

### dir()

获取一个对象的所有属性和方法：

    dir('ABC')

属性：

getattr():

    getattr(obj, 'z') ＃如果属性不存在会报错
    getattr(obj, 'z', 404) # 如果属性不存在就返回 404

setattr():

    setattr(obj, 'y', 19)

hasattr():

    hasattr(obj, 'power')


## 实例属性和类属性

定义一个类属性：

    class Student(object):
        name = 'Student'

类的所有实例都能访问到这个属性

注意不要把实例属性和类属性使用相同的名称，实例属性会屏蔽掉类属性

# 面向对象高级编程

## 使用 __slots

在创建一个class的实例后给实例绑定任何属性和方法：

    class Student(object):
        pass

    s = Student()
    s.name = 'Michael' #绑定属性

    def set_age(self ,age):
        self.age = age
    from types import MethodType
    s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
    s.set_age(25) # 调用方法
    s.age # 25

给一个实例绑定的方法或者属性，对另一个实例是不起作用的

给class绑定方法：

    def set_score(self, score):
        self.score = score

    Student.set_score = set_score

### 使用 __slots__

限制实例的属性，在定义class的时候，定义一个特殊的`__slots__`变量：

    class Student(object):
        __slots_- = ('name', 'age') # 使用tuple定义允许绑定的属性名称

如果绑定没有定义的属性会报错：`AttributeError`

限制对于其子类没有作用

## 使用@property

负责把一个方法变成属性调用：

    class Student(object):
        @property
        def score(self):
            return self._score

        @score.setter
        def score(self ,value):
            if not isinstance(value, int):
                reise ValueError('scroe must be an integer!')
            if value < 0 or value > 100:
                raise ValueError('score must between 0 -100!')
            self._score = value

使用：
    s = Student()
    s.score = 60 # s.set_score(60)
    s.score # s.get_score()

只读属性：上面的类去掉`@score.setter 后面的函数`，score就成了自读属性

## 多重继承

    class Dog(Mammal, Runnable):
        pass

### MixIn

## 定制类
看到类似`__xxx__`的变量或者函数名就要注意这些在Python中是有特殊用途的。

比如：

1. `__slots__`
2. `__len__`
3. `__str__`

        class Student(object):
            def __init__(self, name):
                self.name = name
            def __str__(self):
                return 'Student object (name: %s)' % self.name
            __repr__ = __str__

    `print(Student('Michael'))`:调用`__str__()`
    `s`:调用`__repr__()`

4. `__iter__`:如果一个类被用于`for..in`循环就需要实现这个方法，该方法返回一个迭代对象，然后Python的for循环就会不断调用这个迭代对象的`__next__()`方法获取下一个值，直到遇到`StopIteration`错误时退出循环。

        class Fib(object):
            def __init__(self):
                self.a, self.b = 0, 1
            def __iter__(self):
                return self
            def __next__(self):
                self.a, self.b = self.b, self.a + self.b
                if self.a > 10000:
                    raise StopIteration();
                return self.a

5. `__getitem__`:如果要让对象实现下标操作 `f = Fib()   f[0]`

        class Fib(object):
            def __getitem__(self, n):
                if isinstance(n, int):
                    a, b = 1, 1
                    for x in range(n):
                        a, b = b, a + b
                    return a
                if ininstance(n, slice):
                    start = n.start
                    stop = n.stop
                    if start is None:
                        start = 0
                    a, b = 1, 1
                    L = []
                    for x in range(stop):
                        if x>= start:
                            L.append(a)
                        a, b = b, a + b
                    return L



6. `__setitem__`
7. `__delitem__`
8. `__getattr__`:正常情况调用类没有定义的方法或者属性的时候会报错，可以通过定义这个方法避免一部分
        class Student(object):
            def __init__(self):
                self.name = 'Michael'
            def __getattr__(self, attr):
                if attr == 'score'
                    return 99

    当调用不存在的属性的时候，Python解释器就会调用`__getattr`来尝试获取属性

    这实际上可以把一个类的所有属性和方法调用全部动态化处理了，不需要任何特殊手段。
    作用：可以针对完全动态的情况作调用。

    REST API：链式调用：

        class Chain(object):
            def __init__(self, path=''):
                self._path = path
            def __getattr__(self, path):
                return Chain('%s/%s' % (self.path, path))
            def __str__(self):
                return self._path
            __repr__ = __str__

    使用：

        Chain().status.user.timeline.list # /status/user/timeline/list
    这样，无论API怎么变，SDK都可以根据URL实现完全动态的调用，而且，不随API的增加而改变！
    还有些REST API会把参数放在URL中：GitHub的API
        GET /users/:user/repos
        Chain().users('michael').repos

9. `__call__`:调用自身的方法:

        class Student(object):
            def __init__(self, name):
                self.name = name
            def __call__(self):
                print('My name is %s.' % self.name)

    使用：
        s = Student('Michael')
        s()

    对象能否调用的判断方式：是否是`Callable`对象
        callable(Student()) # True
        callable(max) # True

## 使用枚举类
`Enum`

    from enum import Enum

    Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr'))

定义了`Month`类型的枚举类，使用`Month.Jan`来引用常量，或者枚举所有成员：

    for name, member in Month.__members__.items():
        print(name, '=>', member, ',', member.value)

更精确控制枚举类型：自定义类

    from enum import Enum, unique

    @unique
    class Weekday(Enum):
        Sun = 0
        Mon = 1
        Tue = 2

`@unique`装饰器保证没有重复的值

访问枚举类型的方法：

    day1 = Weekday.Mon
    day2 = Weekday['Tue'] # Weekday.Tue
    day3 = Weekday.Tue.value # 2
    day4 = Weekday(1) # Weekday.Mon

    for name, member in Weekday.__members__.items():
        pass   # Sun => Weekday.Sun


## 使用元类
动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的。

`tyoe()`查看一个类型或者变量的类型：

    type(Hello) # <class 'type'>
    type(h) # <class 'hello.Hello'>

通过`type`创建一个类：

    1. class 的名称
    2. 继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；
    3. class的方法名称与函数绑定，这里我们把函数fn绑定到方法名hello上。

### metaclass
控制类的创建行为的第二种方式

元类：先定义类，然后创建实例，先定义metaclass，然后创建类。

先定义metaclass，就可以创建类，最后创建实例。

metaclass是Python面向对象里最难理解，也是最难使用的魔术代码。正常情况下，你不会碰到需要使用metaclass的情况，所以，以下内容看不懂也没关系，因为基本上你不会用到。

。。跳过

# 错误和调试

## 错误处理

    try:
        print('try...')
        r = 10 / 0
        print('result:', r)
    except ZeroDivisionError as e:
        print('except:', e)
	except:
		pass
    fnally:
        print('finally...')
    print('END')

所有的错误类型都继承自`BaseException`

### 获取错误信息

	e.args[0]

### 异常类 Exception

获取 异常名 包括父类：

	class MySpecialError(RuntimeError):
		pass

	mro = MySpecialError.mro()
	self.assertEqual('RuntimeError', mro[1].__name__)
    self.assertEqual('Exception', mro[2].__name__)
    self.assertEqual('BaseException', mro[3].__name__)
    self.assertEqual('object', mro[4].__name__)



### 调用堆栈
如果错误没有被捕获，它就会一直往上抛，最后被Python解释器捕获

### 错误记录
Python内置`logging`模块记录错误信息：

    # err_logging.py
    import logging

    logging.exception(e)

通过配置，logging还可以把错误记录到日志文件里，方便事后排查。

### 抛出错误
定义自己的错误：

    class FooErrr(ValueError)
        pass
    def foo(s):
        n = int(s)
        if n==0
            raise FooError('invalid value: %s' % s)
        return 10/n

    foo('0')

aise语句如果不带参数，就会把当前错误原样抛出。此外，在except中raise一个Error，还可以把一种类型的错误转化成另一种类型：


## 调试

### 断言

    assert n != 0, 'n is zero!'

断言的意思是这里`n!=0`应该为True，否则程序后面的代码会出错

关闭断言： `-0`

关闭后所有的assert语句被当成pass处理

### loggin

    logging.info() # 输出一段文本
    logging.basicConfig(level=logging.INFO) # 设置logging的级别

logging也可以通过配置，设置输出到文件中

### pdb

让程序单步运行

    python3 -m pdb err.py
    1 # 查看代码
    n # 单步执行代码
    p 变量名 # 查看变量
    q # 结束调试

 ### pdb.set_trace()
 设置断点

    c # 继续运行
## 单元测试
单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。


## 文档测试

# IO编程

## 文件读写

### 读文件
`open()`:

    f = open(`/Users/michael/test.txt`,'r') # r 表示读， 如果文件不存在报错：`IOError` w, a

文件打开成功后：

    f.read() # 返回文件中的 str

关闭文件：

    f.close(()

完整流程：

    try:
        f = open('/path', 'r')
            print(f.read())
    finally:
        if f:
            f.close()


简化，自动关闭：

    with open('/path','r') as  f:
        print(f.read())


read不设置读取量的时候，容易内存溢出，可以设置读取量：

    for line in f.readlines():
        print(line.strip()) # 把末尾 \n 去掉

    f.read(size) # size 读取子节数

### file-like Object
具有`read()`方法的对象

比如:`StringIO`

### 二进制文件

    f = open('/path','rb')
    f.read() # 输出十六进制表示的子节

### 字符编码

    f = open('/open', 'r', encoding='gbk')
    f.read()

遇到编码不规范的文件会`UnicodeDecodeError`:

    f = open('/path', 'r', encoding='gbk', errors='ignore')

### 写文件
写文件和读文件是一样的，唯一区别是调用open()函数时，传入标识符'w'或者'wb'表示写文本文件或写二进制文件

要写入特定编码的文本文件，请给open()函数传入encoding参数，将字符串自动转换成指定编码。


## StringIO和BytesIO

### StringIO
在内存中读写，在内存中读写str，先创建StringIO，然后像文件一样写入即可：

    from io import StringIO
    f = StringIO()
    f.write('hello') # 返回 5 字符数
    f.getvalue() # 获取之前写入的所有字符

    from io import StringIO
    f = StringIO('Hello!\nHi!\nGoodbye!')
    while True:
        s = f.readline()
        if s == '':
            break
        print(s.strop())

### BytesIO
内存中读写二进制，创建一个，然后写入

    from io import BytesIO
    f = BytesIO()
    f.write('中文'.encode('utf-8'))
    print(f.getvalue9)) # 十六进制

    from io import StringIO
    f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
    f.read() # b'\xe4\xb8\xad\xe6\x96\x87'

## 操作文件盒目录

使用`os`模块：

    import os
    os.name # 操作系统 posix:linux,Unix,Mac os; nt: Windows

获取系统详细信息：

    os.uname() # 在 Window 中无效

### 环境变量

    os.environ

获取某个环境变量的值：

    os.environ.get('key')

### 操作文件和目录

操作文件和目录的函数在：`os`,`os.path`模块中：

    # 查看当前目录的绝对路径：
    os.path.abspath('.') # /Users/michael
    # 在某目录下创建一个新目录， 1. 获取完整路径
    os.path.join('/Users/michael','testdir') # '/Users/michael/testdir' 返回目录
    # 2. mkdir
    os.mkdir(‘上面代码创建的目录’)
    # rm
    os.rmkdir('path....')

合成两个路径的时候，不要直接使用字符串拼接，而要通过`os.path.join()`,保证正确处理不同的操作系统的路径分隔符：linux,unix,mac:`/`; Window:`\`

拆分路径：`os.path.split()` 会自动分隔路径和文件名；`os.path.splitext()`分隔文件的扩展名 `('/path/to/file', '.txt')`

文件操作：

    os.rename('test.text','test.py')
    os.remove('test.py')

`os`模块中没有复制文件的操作，因为复制文件不是操作系统提供的系统调用，`shutil`中提供了文件复制的操作，可以看作是'os'模块的补充。

过滤文件：

    [x for x in os.listdir('.') if os.path.isdir(x)] # 获取目录中的所有目录

    [x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='.py'] # 获取目录下的 py 文件


## 序列化
`pickling`,`unpickling`

序列化之后，就可以把序列化后的内容写入磁盘，或者通过网络传输到别的机器上。

Python提供了`pickle`模块来实现序列化：

    import pickle
    d = dict(name='Bob', age=20, score=88)
    pickle.dumps(d) # 方法一 这个方法将对象序列化成 bytes 然后写入文件

    f = open('dump.txt', 'wb') # 方法二
    pickle.dump(d, f)
    f.close()

读取文件中的对象：

    f = open('dump.txt', 'rb')
    d = pickle.load(f)
    f.close() # d: {'age': 20, 'score': 88, 'name': 'Bob'}


### JSON
Python内置的`json`模块： Python对象变成JSON

    import json
    d = dict(name='Bob', age=20, score=88)
    json.dumps(d) #'{"age": 20, "score": 88, "name": "Bob"}'

类似的，dump()方法可以直接把JSON写入一个file-like Object。

JSON to 对象使用`loads()`,`load()`前者把JSON的字符串反序列化，后者从file-like Object中读取字符串并反序列化:

    json_str = '{"age": 20, "score": 88, "name": "Bob"}'
    json.loads(json_str) # {'age': 20, 'score': 88, 'name': 'Bob'}

### JSON 进阶
Python 的 `dict`对象可以直接序列化为JSON的`{}`,不过我们一般使用`class`表示对象：

    s = Student('Bob', 20, 88)
    print(json.dumps(s))

对象实现可序列化：

    def student2dict(std):
        return{
            'name':std.name,
            'age':std.age,
            'score': std.score
        }

这样Student对象会先通过上门的函数转换成`dict`对象，然后被序列化

    print(json.dumps(s, default=student2dict))

    print(json.dumps(s, default=lambda obj: obj.__dict__))

json反序列化：

    def dict2student(d):
        return Student(d['name'], d['age'], d['score'])

# 进程和线程

## 多进程

在Python程序中创建子进程：

    import os
    print('Process (%s) start...' % os.getpid())
    # Only works on Unix/Linux/Mac:
    pid = os.fork()
    if pid ==0:
        print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getpid()))
    else:
        print('I (%s) just created a child precess(%s).' % (os.getpid()), pid)

运行结果：

    Process (876) start...
    I (876) just created a child process (877).
    I am child process (877) and my parent is 876.

由于Window中没有`fork`调用，所以上门的代码在Windows中无效

一个进程在接到新任务时就可以复制出一个子进程来处理新任务，常见的Apache服务器就是由父进程监听端口，每当有新的http请求时，就fork出子进程来处理新的http请求。

### multiprocessing
提供跨平台的多进程模块

启动一个子进程并等待其结束：

    from multiprocessing import Process
    import os

    # 子进程要执行的代码
    def run_proc(name):
        print('Run child process %s (%s)...' % (name, os.getpid()))
    if __name__ == '__main__':
        print('Parent process %s.' % os.getpid())
        p = Process(target=run_proc, args=('test', ))
        print(Child process will start.'')
        p.start()
        p.join()
        print('Child process end.')

执行结果：

    Parent process 928.
    Process will start.
    Run child process test (929)...
    Process end.


创建子进程时，只需要传入一个执行函数和函数的参数，创建一个Process实例，用start()方法启动，这样创建进程比fork()还要简单。

join()方法可以等待子进程结束后再继续往下运行，通常用于进程间的同步。

### Pool
如果要启动大量的子进程，可以用进程池的方式批量创建子进程：



### 子进程
`subprocess`启动一个子进程，然后控制其输入和输出。

    import subprocess

    print('$ nslookup www.python.org')
    r = subprocess.call(['nslookup', 'www.python.org'])
    print('Exit code:', r)


### 进程间通讯
Python的`multiprocessing`模块包装了底层机制，提供`Queue`,`Pipes`等方式来实现交互

    from multiprocess import Process, Queue
    import os, time, random

    # 写数据进进程执行的代码：
    def write(q):
        print('Process to write: %s' % os.getpid())
        for value in ['A','B','C']:
            print('Put %s to queue...' % value)
            q.put(value)
            time.sleep(random.random())

    # 读数据进程执行的代码：
    def read(q):
        print('Process to read: %s' % os.getpid())
        while True:
            value = q.get(True)
            print('Get %s from queue.' % value)

    if __name__=='__main__':
        # 父进程创建Queue，并传给各个子进程：
        q = Queue()
        pw = Process(target = write, args=(q, ))
        pr = Process(target=read, args=(q, ))
        #启动子进程pw，写入：
        pw.start()
        # 启动子进程pr，读取：
        pr.start()
        # 等待pw结束：
        pw.join()
        # pr进程里是死循环，无法等待其结束，只能强行终止：
        pr.terminate()

        Process to write: 50563
        Put A to queue...
        Process to read: 50564
        Get A from queue.
        Put B to queue...
        Get B from queue.
        Put C to queue...
        Get C from queue.

## 多线程

`_thread`:低级模块
`threading`:高级模块，对`_thread`的封装

    import time, threading

    # 新线程执行的代码：
    def loop():
        print('thread %s is running...' % threading.current_thread().name)
        n = 0
        while n < 5:
            n = n + 1
            print('thread %s >>> %s' % (threading.current_thread().name,n))
            time.sleep(1)
        print('thread %s ended.' % threading.current_thread().name)

    print('thread %s is running...' % threading.current_thread().name)
    t = threading.Thread(target=loop, name='LoopThread')
    t.start()
    t.join()
    print('thread %s ended.' % threading.current_thread().name)


    thread MainThread is running...
    thread LoopThread is running...
    thread LoopThread >>> 1
    thread LoopThread >>> 2
    thread LoopThread >>> 3
    thread LoopThread >>> 4
    thread LoopThread >>> 5
    thread LoopThread ended.
    thread MainThread ended.

任何进程都有一个默认线程：主线程（MainThread）

### Lock
多线程中，所有变量都由所有线程共享,并发问题解决：

    balance = 0
    lock = threading.Lock()

    def run_thread(n):
        for i in range(100000):
            # 先获取锁
            lock.acquire()
            try:
               change_it(n)
            finally:
                lock.release()

### 多核CPU


## ThreadLocal

在多线程环境下，每个线程都有自己的数据。一个线程使用自己的局部变量比使用全局变量好，因为局部变量只有线程自己能看见，不会影响其他线程，而全局变量的修改必须加锁。

    import threading
    # 创建全局ThreadLocal对象
    local_school = threading.local()

    def process_student():
        # 获取当前线程关联的student
        std = local_school.student
        print('Hello, %s(in %s)' % (std, threading.current_thread().name))
    def process_thread(name):
        # 绑定ThreadLocal的student：
        local_school.student = name
        process_student()

    t1 = threading.Thread(target=process_thread, args=('Alice',) name='Thread-A')
    t2 = threading.Thread(target=process_thread, args=('Bob', ) name='Thread-B')
    t1.start()
    t2.start()
    t1.join()
    t2.join()

    # 结果
    Hello, Alice (in Thread-A)
    Hello, Bob (in Thread-B)


## 进程 vs. 线程

Master-Worker模式，Master负责分配任务，Worker负责执行任务。

对进程模式：最大优点稳定性高，进程奔溃不回影响其他进程，但是性能消耗大

多线程模式：比多进程快一点，但是任何一个线程奔溃会影响整个进程

### 线程切换
线程或者进程过多，切换回耗时影响性能。



## 分布式进程

# 常用内建模块

## sys

```python
import sys

print('The command line arguments are:')

for i in sys.argv:
    print(i)

# python using_sys.py we are arguments
# result: using_sys.py   we are arguments 输入的参数
```

## __name__ 属性

每个模块都有一个名字，并且通过模块中的某些语句可以得到这个模块名。

在一些想要搞清模块是独立运行还是被导入的情况下，这会非常方便。

当模块第一次被导入时模块中的代码会被执行。

据此改变模块独立执行时的行为方式。（注：独立运行是指程序最开始运行的那个脚本文件(/模块)）

每个python模块都有自己的__name__定义，如果它是’__main__’则暗示模块为独立运行，我们可以进行一些适当的处理。

```python
if __name__ == '__main__':
    print('This program is being run by itself')
else:
    print('I am being imported form another module')
```

## 创建自己的模块

```python
#!/usr/bin/python

# Filename: mymodule.py

def sayhi():

    print('Hi, this is mymodule speaking.')

__version__ = '0.1'
```

##

## datetime
Python处理日期和时间的标准库

### 获取当前日期和时间

    from datetime import datetime
    now = datetime.now()
    print(now) # 2015-05-18 16:28:07.198690
    print(type(now)) # <class 'datetime.datetime'>

`datetime`模块还包有`datetime`类

### 获取指定日期和时间

    from datetime import datetime
    dt = datetime(2016, 8, 18, 12, 23)
    print(dt) # 2016-08-18 12:23:00

### datetime 转换成 timestamp

    from datetime import datetime
    dt = datetime(2016, 8, 18, 23, 23)
    dt.timestamp() # 14232323232.0

### timestamp --> datetime

    from datetime import datetime
    t = 1429417200.0
    print(datetime.fromtimestamp(t)) # 2015-04-19 12:20:00 local time

注意到timestamp是一个浮点数，它没有时区的概念，而datetime是有时区的。上述转换是在timestamp和本地时间做转换。

    datetime.utcfromtimestamp(t) # UTC time


### str --> datetime

    from datetime import datetime
    cday = datetime.strptime('2015-6-1 18:19:23', '%Y-%m-%d %H:%M:%S')
    print(cday) #2015-6-1 18:19:23

### datetime --> str

    form datetime import datetime
    now = datetime.now()
    print(now.strftime('%a, %b %d %H:%M')) # Mon, May 05 16:28

### datetime加减

    from datetime import datetime, timedelta
    now = datetime.now()
    now + timedelta(hours=10)
    now - timedelta(days=1)
    now + timedelta(days=2, hours=12)

### 本地时间 --> UTC

    from datetime import datetime, timedelta, timezone
    tz_ut_8 = timezone(dimedelta(hours=8)) # 创建时区UTC＋8:00

## collections

Python内建的一个集合模块，提供许多有用的集合类。

### namedtuple
重命名一个`tuple`:

    from collections import namedtuple
    Point = namedtuple('Point', ['x', 'y'])
    p = Point(1, 2)
    p.x # 1
    p.y # 2

`namedtuple`是一个函数，用来自定义`tuple`对象，并且规定了`tuple`元素的个数，可以用属性而不是索引来引用`tuple`的某个元素

    isinstance(p, Point) # True
    isinstance(p, tuple) # True

使用坐标和半径表示一个圆：

    Circle = namedtuple('Circle', ['x', 'y', 'r'])


### deque
使用`list`存储数据时，按索引查询访问元素很快，但是插入和删除元素就很慢。

`deque`是为了实现高效插入和删除操作的双向列表，适合于队列和栈：

    from collections import deque
    q = deque(['a', 'b', 'c'])
    q.append('x')
    q.appendleft('y')
    # q.append(.) q.pop(.) q.appendleft(.) q.popleft(.)


### defalutdict
使用`dict`时，如果Key不存在就会报错`KeyError`,如果希望Key不存在的时候返回默认值：

    from collections import defalutdict
    dd = defaultdict(lambda: 'N/A')
    dd['Key1'] = 'abc'
    dd['key1'] # 'abc'
    dd['key2'] # 'N/A'

### OrderedDict

使用`dict`时，Key是无序的，在对`dict`作迭代的时候无法确定Key的顺序，如果要保持Key的顺序：

    from collections import OrderedDict
    d = dict([('a', 1), ('b', 2), ('c', 3)])
    d # dict key 是无序的
    od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
    # key 有序 按照插入的顺序排列

### Counter
简单的计数器

统计字符出现的次数：

    from collections import Counter
    c = Counter()
    for ch in 'programming'
        c[ch] = c[ch] +1
    c # Counter({'g': 2, 'm': 2, 'r': 2, 'a': 1, 'i': 1, 'o': 1, 'n': 1, 'p': 1})

实际是一个`dict`的子类

## base64

Base64 编码：使用64个字符来表示任意二进制

    import base64
    base64.b64encode(b'binary\x00string')# b'YmluYXJ5AHN0cmluZw=='
    base64.b64decode(b'YmluYXJ5AHN0cmluZw==') #b'binary\x00string'

url safe 的base64编码：使用`-`,`_`代替`+`,`/`

    base64.urlsafe_b64encode(b'i\xb7\x1d\xfb\xef\xff')
    base64.urlsafe_b64decode('abcd--__')

## struct

处理字节


	from struct import *

	packed_data = pack('ii', 12, 43)
	print(packed_data) # b'.......' 以字节存储数据


## hashlib

提供常见的摘要算法：

    import hashlib
    md5 = hashlib.md5()
    md5.update('content........'.encode('utf-8'))
    print(md5.hexdigest()) # d26a53750bc40b38b65a520292f69306

如果数据量大，可以分块调用`update()`,最后计算的结果是一样的

生成结果是固定的128 bit字节，通常用一个32位的16进制字符串表示。

SHA1:使用方法一样

    import hashlib
    sha1 = hashlib.sha1()
    sha1.update('content,......'.encode('utf-8'))
    sha1.hexdigest()

SHA1的结果是160 bit字节，通常用一个40位的16进制字符串表示。

### 应用

保存用户的密码，保存的是密码的摘要算法

## iterrools
操作迭代对象的函数

提供的`无限`迭代器：

    import itertools
    natuals = itertools.count(1)
    for n in natuals:
        print(n)

`count()`会创建一个无限的迭代器，所以上述代码会打印出自然数序列，根本停不下来，只能按Ctrl+C退出。

    import itertools
    cs = itertools.cycler('ABC')
    for c incs:
        print(c)

`cycler()`会循环遍历

`repeat('A', 3)` 限定次数循环

使用`takewhile()`等函数可以截取无限序列：

    natuals = itertools.count(1)
    ns = itertools.takewhile(lambda x: x <= 10, natuals)
    list(ns) # [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

### chain()
把一组迭代对象串联起来，形成一个更大的迭代器：

    for c in itertools.chain('ABC', 'XYZ') # 迭代效果：'A' 'B' 'C' 'X' 'Y' 'Z'

### groupby()

把迭代器中相邻的重复元素挑出来放在一起:

    for key, group in itertools.groupby('AAABBBCCAAA'):
        print(key, list(group)) # A ['A', 'A', 'A'] B ['B', 'B', 'B'] C ['C', 'C'] A ['A', 'A', 'A']

挑选规则是由一个函数完成的： 忽略大小写

    for key, group in itertools.groupby('AaaBBbcCAAa', lambda c: c.upper()):
        print(key, list(group))

模块提供的全部是处理迭代功能的函数，它们的返回值不是list，而是Iterator，只有用for循环迭代的时候才真正计算。





## XML

## HTMLParser

## urllib

操作URL的功能

### Get
`request`模块抓取URL内容：

    from urllib import request

    with request.urlopent('https://api.douban.com/v2/book/2129650') as f:
        data = f.read()
        print('Status:', f.status, f.reason)
        for k, v in f.getheaders():
            print('%s: %s' % (k, v))
        print('Data:', data.decode('utf-8'))

结果：

    Status: 200 OK
    Server: nginx
    Date: Tue, 26 May 2015 10:02:27 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 2049
    Connection: close
    Expires: Sun, 1 Jan 2006 01:00:00 GMT
    Pragma: no-cache
    Cache-Control: must-revalidate, no-cache, private
    X-DAE-Node: pidl1
    Data: {"rating":{"max":10,"numRaters":16,"average":"7.4","min":0},"subtitle":"","author":["廖雪峰编著"],"pubdate":"2007-6","tags":[{"count":20,"name":"spring","title":"spring"}...}


模拟游览器发送GET请求，使用`Request`对象，通过加请求头伪装成游览器:

    form urllib import request

    req = request.Request('http://www.douban.com/')
    req.add_header('User-Agent', 'Mozilla/6.0 (iPhone; CPU iPhone OS 8_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Version/8.0 Mobile/10A5376e Safari/8536.25')
    with request.uelopen(req) as f:
        print('Status:', f.status, f.reason)
        for k, v in f.getheaders():
            print('%s: %s' % (k, v))
        print('Data:', f.read().decode('utf-8'))

    #  返回html


### Post

把`data`以bytes形式传入：

    from urllib import request, parse

    print('Login to weibo.cn...')
    email = input('Email: ')
    passwd = input('Password: ')
    login_data = parse.urlencode([
        ('username', email),
        ('password', passwd),
        ('entry', 'mweibo'),
        ('client_id', ''),
        ('savestate', '1'),
        ('ec', ''),
        ('pagerefer', 'https://passport.weibo.cn/signin/welcome?entry=mweibo&r=http%3A%2F%2Fm.weibo.cn%2F')
    ])

    req = request.Request('https://passport.weibo.cn/sso/login')
    req.add_header('Origin', 'https://passport.weibo.cn')
    req.add_header('User-Agent', 'Mozilla/6.0 (iPhone; CPU iPhone OS 8_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Version/8.0 Mobile/10A5376e Safari/8536.25')
    req.add_header('Referer', 'https://passport.weibo.cn/signin/login?entry=mweibo&res=wel&wm=3349&r=http%3A%2F%2Fm.weibo.cn%2F')

    with request.urlopen(req, data=login_data.encode('utf-8')) as f:
        print('Status:', f.status, f.reason)
        for k, v in f.getheaders():
            print('%s: %s' % (k, v))
        print('Data:', f.read().decode('utf-8'))


### Handler

更复杂的控制

# 常用第三方模块

## PIL

Python Imaging Library:Python平台上图像处理标准库

升级版：Pillow for python3

### 安装

    pip install pillow

### 操作图像

缩放操作：

    from PIL import Image

    # 打开jpg图像文件
    im = Image.open('test.jpg')
    # 获取图像尺寸
    w ,h = im.size
    # 缩放
    im.thumbnail((w//2, h//2))
    # 把缩放后的图像保存
    im.save('thumbnail.jpg, 'jpeg'')

    # 模糊
    from PIL import Image, ImageFilter
    im = Image.open('test.jpg')
    im2 = im.filter(ImageFilter.BLUR)
    im2.save('blur.jpg', 'jpeg')

    # 使用 ImageDraw 提供一系列绘图方式， 比如生成字母验证
    from PIL import Image, ImageDraw, ImageFont, ImageFilter
    import random

    # 随机字母
    def rndChar():
        return chr(rangdom.randint(65, 90))
    # 随机颜色
    def rndColor():
        return (random.randint(64, 255), random.randint(64, 255), random.randint(64, 255))
    # 随机颜色2
    def rndColor2():
        return (random.randint(32, 127), random.randint(32, 127), random.randint(32, 127))
    # 240 * 60
    width = 60 * 4
    height = 60
    image = Image.new('RGB', (width, height), (255,255,255))
    # 创建 Font 对象
    font = ImageFont.truetype('Arial.ttf', 36)
    # 创建 Draw 对象
    draw = ImageDraw.Draw(image)
    # 填充每个像素
    for x in range(width):
        for y in range(height):
            draw.point((x, y), fill=rndColor())
    # 输出文字
    for t in range(4):
        draw.text((60 * t + 10, 10), rndChar(), font=font, fill=rndColor2())
    # 模糊
    image = image.filter(ImageFilter.BLUR)
    image.save('code.jpg', 'jpeg')

### crop

	 img = Image.open('demo.png')
	 area = (100, 100, 300, 300)
	 cropped_img = img.crop(area)
	 cropped_img.show()

### combine

	one = Image.open('one.png')
 	two = Image.open('two.png')
	area = (100, 100, 300, 300)
	one.paste(two, area)



# 图形界面
第三方库：

- TK
- wxWidgets
- QT
- GTK

## Tkinter

    from tkinter import *

    class Application(Frame):
        def __init__(self, master=None):
            Frame.__init__(self, master)
            self.pack()
            self.createWidgets()

        def createWidgets(self):
            self.helloLabel = Label(self, text='Hello, world!')
            self.helloLabel.pack()
            self.quitButton = Button(self, text='Quit', command=self.quit)
            self.quitButton.pack()

    app = Application()
    app.master.title('title')
    app.mainloop() # 主消息循环主线程负责监听来自操作系统的消息，并依次处理每一条消息。因此，如果消息处理非常耗时，就需要在新线程中处理。

# 网络编程

## TCP/IP 简介

通讯：双方必须知道对方的标识

标识：IP地址

IP协议负责把数据从一台计算机通过网络发送给另一个计算机

TCP协议建立在IP协议之上：TCP协议负责在两台计算机之间建立可靠连接，保证数据包按顺序到达。TCP协议会通过握手建立连接，然后，对每个IP包编号，确保对方按顺序收到，如果包丢掉了，就自动重发。

HTTP,SMTP协议建立在TCP协议之上

端口：指定程序


## TCP编程

### 客户端
可靠连接TCP

    import socket

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(('www.baidu.com', 80))

创建`Socket`时：指定IPv4`AF_INET`协议，`SOCK_STREAM`指定使用面向流的TCP协议

80端口是Web服务的标准端口

SMTP服务是25端口

FTP服务是21端口

端口号小于1024的是Internet标准服务的端口，端口号大于1024的，可以任意使用。

    # 发送数据:
    s.send(b'GET / HTTP/1.1\r\nHost: www.sina.com.cn\r\nConnection: close\r\n\r\n')
    # 接收数据
        buffer = []
        while True:
            d = s.recv(1024)
            if d:
                buffer.append(d)
            else:
                break
        data = b''.join(buffer)
        # 关闭连接:
        s.close()

接收到数据包括HTTP头和网页本身：

    header , html = data.split(b'\r\n\r\n', 1)
    print(header.devode('utf-8'))
    with open('sina.html', 'wb') as f:
        f.write(html)

### 服务器
服务器进程首先要绑定一个端口并监听来自其他客户端的连接。

一个Socket依赖4项：服务器地址、服务器端口、客户端地址、客户端端口来唯一确定一个Socket。

但是服务器还需要同时响应多个客户端的请求，所以，每个连接都需要一个新的进程或者新的线程来处理

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

绑定地址和端口，服务器上可能有多快网卡，绑定多有网络地址：`0.0.0.0`,绑定本机地址`127.0.0.1`

    s.bind('127.0.0.1', 9999)
    s.listen(5) # 传入的参数指定等待连接的最大数量
    print('Waiting for connection...')

服务器程序通过一个永久循环来接受来自客户端的连接，accept()会等待并返回一个客户端的连接

    while True:
        sock, addr = s.accept()
        t = threading.Thread(target=tcpling, args=(aock, addr))
        t.start()

每个连接都必须创建新线程（或进程）来处理，否则，单线程在处理连接的过程中，无法接受其他客户端的连接：

    def tcpling(sock, addr):
        print('Accept new connection from %s:%s...' % addr)
        sock.send(b'Welcome!')
        while True:
            date = sock.recv(1024)
            time.sleep(1)
            if not data or data.decode('utf-8') == 'exit':
                break
            sock.send(('Hello, %s!' % data.decode('utf-8')).encode('utf-8'))
        sock.close()
        print('Connection from %s:%s closed.' % addr)

同一个端口，被一个Socket绑定了以后，就不能被别的Socket绑定了。

## UDP 编程
TCP是建立可靠连接，并且通信双方都可以以流的形式发送数据。相对TCP，UDP则是面向无连接的协议。

速度快

服务器：

    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    s.bind(('127.0.0.1', 9999))

    print('Bind UDP on 9999...')
    while True:
        # 接收数据: 这里省掉了多线程
        data, addr = s.recvfrom(1024)
        print('Received from %s:%s.' % addr)
        s.sendto(b'Hello, %s!' % data, addr)

客户端：

    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    for data in [b'Michael', b'Tracy', b'Sarah']:
        # 发送数据:
        s.sendto(data, ('127.0.0.1', 9999))
        # 接收数据:
        print(s.recv(1024).decode('utf-8'))
    s.close()

# 电子邮件


# 访问数据库

- Oracle，典型的高富帅；
- SQL Server，微软自家产品，Windows定制专款；
- DB2，IBM的产品，听起来挺高端；
- Sybase，曾经跟微软是好基友，后来关系破裂，现在家境惨淡
- MySQL，大家都在用，一般错不了；
- PostgreSQL，学术气息有点重，其实挺不错，但知名度没有MySQL高；
- sqlite，嵌入式数据库，适合桌面和移动应用

## 使用SQLite
SQLite是一种嵌入式数据库，它的数据库就是一个文件。

SQLite本身是C写的，而且体积很小，所以，经常被集成到各种应用程序中

Python就内置了SQLite3

要操作关系数据库，首先需要连接到数据库，一个数据库连接称为Connection

连接到数据库后，需要打开游标，称之为Cursor，通过Cursor执行SQL语句，然后，获得执行结果。

    import sqlite3
    # 连接到SQLite数据库 数据库文件 test.db 文件不存在会创建
    conn = sqlites.connect('test.db')
    # 创建一个 Cursor
    cursor = conn.cursor()
    # 执行 SQL语句
    cursor.execute('create table user (id varchar(20) primary key, name varchar(20))')
    # 继续执行一条SQL语句，插入一条记录:
    cursor.execute('insert into user (id, name) values (\'1\', \'Michael\')')
    # 通过rowcount获得插入的行数:
    cursor.rowcount

    cursor.close()
    conn.commit()
    conn.close()

查询：

    conn = sqlite3.connect('test.db')
    cursor = conn.cursor()
    cursor.execute('select * from user where id=?', ('1',))
    values = cursor.fetchall()
    # 结果集

    cursor.close()
    conn.close()

一定要注意关闭`Connection`,`Cursor`

`Cursor`执行`insert`,`update`,`delete`语句，结果`rowcount`返回影响行数

`Curosr`执行`select`,通过`featchall()`拿到结果集：list，每个元素是tuple对应一行纪录

    cursor.execute('select * from user where name=? and pwd=?', ('abc', 'password'))

    try:...except:...finally:...


## MySQL
MySQL是为服务器端设计的数据库，能承受高并发访问，同时占用的内存也远远大于SQLite。

### 安装MySQL

下载 安装

配置：`/etc/my.cnf`或者`/etc/mysql/my.cnf`:

    [client]
    default-character-set = utf8
    [mysqld]
    default-storage-engine = INNODB
    character-
	-server = utf8
    collation-server = utf8_general_ci

重启MySQL后，可以通过MySQL的客户端命令行检查编码：

    mysql -u root -p

    mysql> show variables like '%char%';

### 安装MySQL驱动

    pip install mysql-connector-python --allow-external mysql-connector-python

连接数据库：

    import mysql.connector
    conn = mysql.connector.connect(user='root', password='password', database='test')
    cursor = conn.cursor()
    cursor.execute('create table user (id varchar(20) primary key, name varchar(20))')
    cursor.execute('insert into user (id, name) values (%s, %s)', ['1', 'Michael'])
    cursor.rowcount # 1
    conn.commit()
    curcor.close()
    # select
    cursor = conn.cursor()
    cursor.execute('select * from user where id = %s', ('1', ))
    values = cursor.fetchall()
    values #[('1', 'Michael')]
    cursor.close()
    conn.close()

## SQLAlchemy

Object-Relational Mapping，把关系数据库的表结构映射到对象上

    pip install sqlalchemy


第一步，导入SQLAlchemy，并初始化DBSession：

    # 导入:
    from sqlalchemy import Column, String, create_engine
    from sqlalchemy.orm import sessionmaker
    from sqlalchemy.ext.declarative import declarative_base

    # 创建对象的基类:
    Base = declarative_base()

    # define User Object:
    class User(Base):
        # table name:
        __tablename__ = 'user'

        # table construct
        id = Column(String(20), primary_key=True)
        name = Column(String(20))

    # create db connection
    engine = create_engine('mysql+mysqlconnector://root:password@localhost:3306/test')
    # create DBSession :
    DBSession = sessionmaker(bind=engine)

以上代码完成SQLAlchemy的初始化和具体每个表的class定义。如果有多个表，就继续定义其他class，例如School：

    class School(Base):
        __tablename__ = 'school'
        id = ...
        name = ...

`create_engine()`用来初始化数据库连接，SQLAlchemy用一个字符串表示连接信息：

    '数据库类型+数据库驱动名称://用户名:口令@机器地址:端口号/数据库名'

如何添加纪录：

    # create session object
    session = DBSession()
    # create User object
    new_user = User(id='5', name='Bob')
    # add to session:
    session.add(new_user)
    # commit to db
    session.commit()
    # close session
    session.close()

DBSession对象可视为当前数据库连接。

查询数据：

    # create Session:
    session = DBSession()
    # create Query,filter是where条件，最后调用one()返回唯一行，如果调用all()则返回所有行:
    user = session.query(User).filter(User.id='5').one()
    # print
    print('type:', type(user))
    print('name:', user.name)
    # close
    session.close()

ORM框架的作用就是把数据库表的一行记录与一个对象互相做自动转换。

    class User(Base):
        __tablename__ = 'user'

        id = Column(String(20), primary_key=True)
        name = Column(String(20))
        # 一对多:
        books = relationship('Book')

    class Book(Base):
        __tablename__ = 'book'

        id = Column(String(20), primary_key=True)
        name = Column(String(20))
        # “多”的一方的book表是通过外键关联到user表的:
        user_id = Column(String(20), ForeignKey('user.id'))

# Web 开发

## HTTP 协议简介
而浏览器和服务器之间的传输协议是HTTP

- HTML是一种用来定义网页的文本，会HTML，就可以编写网页；
- HTTP是在网络上传输HTML的协议，用于浏览器和服务器的通信

Chrome开发者工具：

- elements 显示网页结构
- Netword 显示游览器和服务器的通信


### HTTP 格式
文本协议

每个Header一行，换行符：`\r\n`:
    GET /path HTTP/1.1
    Header1: Value1
    Header2: Value2
    Header3: Value3

POST:

    POST /path HTTP/1.1
    Header1: Value1
    Header2: Value2
    Header3: Value3

    body data goes here...

两个换行符隔开请求头和请求体

## HTML简介
一套语法规则



## WSGI 接口

web 应用的本质：

1. 游览器发送一个HTTP请求
2. 服务器收到请求生成一个HTML文档
3. 服务器吧HTML文档作为HTTP响应的Body发送给游览器
4. 游览器受到HTTP响应，从HTTP Body 取出HTML 文档并显示

Web Server Gateway Interface

    def application(eviron, start_response):
        start_response('200 OK', [('Content-Type', 'text/html')])
        return [b'<h1> Hello, web!</h>']

上面定义的函数`application`就是符合WSGI标准的一个HTTP处理函数，参数：

1. environ: 一个包含所有HTTP请求信息的`dict`对象
2. start_response:一个发送HTTP响应的函数， 调用就是发送了HTTP响应的Header（Header只能发送一次）
3. 返回的内容就是Body
4. 这个函数由WSGI服务器调用

Python内置的WSGI服务器，wsgirefwsgi

    from wsgiref.simple_server import make_server
    httpd = make_server('', 8000, application)
    print('serving Http on port 8000')

    httpd.serve_forever()

## 使用Web框架

其实一个Web App，就是写一个WSGI的处理函数，针对每个HTTP请求进行响应。

使用WSGI提供的接口来实现实际的项目代码无法维护，需要使用Web框架来处理URL到函数的映射

这里使用`Flash`:

安装：

    pip install flaskla

`flash`同欧Python的装饰器在内部自动把URL和函数关联在一起


## 使用模版

有了Web框架，我们只需不断编写函数带上URL就可以Web App的开发了

那么HTML页面如何生成？ 可以使用模版来生成HTML页面

MVC：模型，视图，控制器

Python处理URL的函数就是C：负责业务逻辑，比如检查用户名是否存在，取出用户信息等

模版就是V：复制界面展示

Model就是C处理完成的数据，C将M传给V：一般是个`dict`，因为Python支持关键字参数，很多Web框架允许传入关键字参数，然后，在框架内部组装出一个dict作为Model。


有了MVC，我们就分离了Python代码和HTML代码。HTML代码全部放到模板里，写起来更有效率。


# 异步IO

异步IO模型需要一个消息循环，在消息循环中，主线程不断重复“读取消息－处理消息”这一过程：

    loop = get_event_loop()
    while True:
        event = loop.get_event()
        process_event(event)

这样的消息模型最早应用在桌面应用程序中，一个GUI程序的主线程就负责不停地读取消息并处理消息。所有的键盘和鼠标等消息都被发送到GUI程序的消息队列中，由GUI程序的主线程处理

## 协程
微线程，Coroutine

协程和线程比：

1. 协程切换没有线程切换的开销，性能优势明显。
2. 不需要多线程的锁机制，因为只有一个线程

利用多核CPU：多进程＋协程

Python对协程的支持通过generator实现

生产者消费者模型：

    def consumer():
        r = ''
        while True:
            n = yield r
            if not n:
                return
            print('[CONSUMER] Consuming %s...' % n)
            r = '200 OK'

    def produce(c):
        c.send(None)
        n = 0
        while n < 5
            n = n + 1
            print('[PRODUCER] Producing %s...' % n)
            r = c.send(n)
            print('[PRODUCER] Consumer return: %S' % r)
        c.close()

    c = consumer()
    produce(c)

1. `consumer` 是一个 `generator` ，把`consumer` 传入`produce`
2. 调用`c.send(None)` 启动生成器
3. 一旦生产了东西后，通过`c.send(n)`切换到`consumer`执行
4. `consumer`通过`yield`拿到消息，处理，又通过`yield`把结果传回
5. `produce`拿到`consumer`处理结果后，继续生产下一条消息
6. `produce`决定不生产后，通过`c.close()`关闭`consumer`，结束整个过程

子线程就是协程的一种特例。

## asyncio
Python 3.4 版本引入的标准库，内置了对异步IO的支持

它的编程模型就是一个消息循环，直接从`asyncio`模块获取一个`EventLoop`的引用，然后把需要执行的协程传入执行就可以实现异步IO

    import asyncio

    @asyncio.coroutine
    def hello():
        print('Hello world!')
        # 异步调用 asyncio.sleep(i):
        r = yield from asyncio.sleep(1)
        print('Hello again!')

    # gaint EventLoop
    loop = asyncio.get_evet_loop()
    # execute coroutine
    loop.run_until_complete(hello())
    loop.close()

使用`@asyncio.coroutine`把一个`generator`标记为一个`coroutine`类型，然后把`coroutine`放到`EventLoop`中执行

还可以使用task把多个`coroutine`大包并发执行

    tasks = [hello(), hello()]
    loop.run_until_complete(asyncio.wait(takse))

## async/await
`asyncio`提供`@asyncio.coroutine`把一个generator标记为coroutine类型,然后在coroutine内部用`yield from`调用另一个coroutine实现异步操作，Python3.5 引入新语法`async`,`await`

1. `@asyncio.coroutine`替换成`async`
2. `yield from`-->`await`

        async def hello():
            print('Hello world')
            r = await asyncio.sleep(1)
            print('Hello again!')

## aiohttp
实现单线程并发IO操作，基于`asyncio`实现的http框架

安装：

    pip install aiohttp

编写Http服务器：

    import asyncio

    from aiohttp import web

    async def index(request):
        await asyncio.sleep(0.5)
        text = '<h>Index</h>'
        return web.Response(body=text.encode('utf-8'))

    async def hello(request):
        await asyncio.sleep(0.5)
        text = '<h>hello, %s!</h1>' % request.math_info('name')
        return web.response(body=text.encode('utf-8'))

    async def init(loop):
        app = web.Application(loop=loop)
        app.router.add_route('GET', '/', index)
        app.router.add_route('GET', '/hello/{name}', hello )
        srv = await loop.create_server(app.make_handler(), '127.0.0.1', 8000)
        print('Server started at http://127.0.0.1:8000...')
        return srv

    loop = asyncio.get_event_loop()
    loop.run_until_complete(init(loop))
    loop.run_forever()

注意aiohttp的初始化函数init()也是一个coroutine，loop.create_server()则利用asyncio创建TCP服务。
