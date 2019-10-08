---
title: Regular Expression
date: 2016-09-04 13:18:12
tags: [Regular Expression, Python]
category: Python

---

参考：[http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)

## 作用

- 判断一段文本是否符合规则
- 从一段文本内容中提取子段

## 匹配大致过程

- 依次拿出表达式和文本中字符比较
- 如果每个字符都能匹配，则匹配成功；一旦有匹配不成功的字符则匹配失败
- 如果表达式中有量词或边界，这个过程会稍有不同


<!--more-->

## 语法 

![正则表达式图](http://qiniu.cuiqingcai.com/wp-content/uploads/2015/02/20130515113723855.png)

正则表达式相关注解

1. 数量词的贪婪模式与非贪婪模式

    贪婪：默认匹配尽可能多的字符；
    非贪婪：匹配尽可能少的字符
    Python中默认是匹配尽可能多的字符

    注：一般使用非贪婪模式来提取字符

2. 反斜杠问题

    Python使用`r`可以解决

## Python re 模块
Python内置模块，支持了正则表达是的支持

常用方法：

    # 返回 pattern 对象
    re.compile(string[, flag])
    # 以下匹配所用函数
    re.match(pattern, string[,flags])
    re.search(pattern, string[, flags])
    re.split(pattern, string[, flags])
    re.findall(pattern, string[, flags])
    re.finditer(pattern, string[, flags])
    re.sub(pattern. string[, flags])
    re.subn(pattern, repl, string[, count])

pattern:匹配模式，匹配规则

flag：匹配模式，可选值

    re.I(全拼：IGNORECASE)忽略大小写（括号内是完整写法，下同）
    re.M(全拼：MULTILINE) 多行模式，改变'^'和'$'的行为（参见上图）
    re.S(全拼：DOTALL) 点任意匹配模式，改变'.'的行为
    re.L(全拼：LOCALE) 使预定字符类 \w \W \b \B \s \S 取决于当前区域设定
    re.U(全拼：UNICODE) 使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性
    re.X(全拼：VERBOSE) 详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释。

### re.match(pattern, string[, flags])

返回一个Match对象

属性：

1. string：匹配时使用的文本
2. re：匹配时使用的Pattern对象
3. pos：文本中正则表达式开始搜索的索引。值与Pattern.match()和Pattern.search()方法的同名参数相同
4. endpos：文本中正则表达式结束搜索的索引。值与Pattern.match()和Pattern.seach()方法的同名参数相同。
5. lastindex：最后一个被捕获的分组在文本中的索引。如果没有被捕获的分组，为None
6. lastgroup：最后一个被捕获的分组的别名。如果这个分组没有别名或者没有被捕获的分组，为None

方法：

1. group([group1 ,...])

    获取一个或多个分组截获的字符串；指定多个参数时将以元组形式返回。group1可以使用编号也可以使用别名；编号0代表整个匹配的子串；不填写参数时，返回group(0)；没有截获字符串的组返回None；截获了多次的组返回最后一次截获的子串。

2. groups([default])
    
    以元组形式返回全部分组截获的字符串。相当于调用group(1,2,…last)。default表示没有截获字符串的组以这个值替代，默认为None。

3. groupdict([default]):

    返回以有别名的组的别名为键、以该组截获的子串为值的字典，没有别名的组不包含在内。default含义同上。

4. start([group]):

    返回指定的组截获的子串在string中的起始索引（子串第一个字符的索引）。group默认值为0。

5. end([group]):

    返回指定的组截获的子串在string中的结束索引（子串最后一个字符的索引+1）。group默认值为0。

6. span([group]):

    返回(start(group), end(group))。

7. expand(template):

    将匹配到的分组代入template中然后返回。template中可以使用\id或\g、\g引用分组，但不能使用编号0。\id与\g是等价的；但\10将被认为是第10个分组，如果你想表达\1之后是字符’0’，只能使用\g0。

### re.search(pattern, string[, flags])

与match区别是：它不需要string从开始位置匹配，match要求string从开始位置匹配

### re.split(pattern, string[, maxsplit])
使用匹配的子串，将文本切割

    import re

    pattern = re.compile(r'\d+')
    print re.split(pattern,'one1two2three3four4')

    ### 输出 ###
    # ['one', 'two', 'three', 'four', '']

### re.findall(pattern, string[, flags])
搜索string，以列表形式返回子串

    import re

    pattern = re.compile(r'\d+')
    print re.split(pattern,'one1two2three3four4')

    ### 输出 ###
    # ['1', '2', '3', '4']

### re.finditer(pattern, string[, flags])

搜索string，返回一个顺序访问每一个匹配结果（Match对象）的迭代器。

    import re

    pattern = re.compile(r'\d+')
    for m in re.finditer(pattern,'one1two2three3four4'):
        print m.group(),

    ### 输出 ###
    # 1 2 3 4

### re.sub(pattern, repl, string[, count])

使用repl替换string中每一个匹配的子串后返回替换后的字符串。

当repl是一个字符串时，可以使用\id或\g、\g引用分组，但不能使用编号0。

当repl是一个方法时，这个方法应当只接受一个参数（Match对象），并返回一个字符串用于替换（返回的字符串中不能再引用分组）。

count用于指定最多替换次数，不指定时全部替换。

    import re

    pattern = re.compile(r'(\w+) (\w+)')
    s = 'i say, hello world!'
     
    print re.sub(pattern,r'\2 \1', s)
      
    def func(m):
        return m.group(1).title() + ' ' + m.group(2).title()
           
    print re.sub(pattern,func, s)
            
    ### output ###
    # say i, world hello!
    # I Say, Hello World!

### re.subn(pattern, repl, string[, count])


