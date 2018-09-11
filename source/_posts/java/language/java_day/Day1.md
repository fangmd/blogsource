---
title: java -- day1
date: 2015-12-28 21:18:12
tags: [java,day]
categories: java基础
---

# Day1
## 基础常识
### 什么是软件
>软件是一系列按照特定顺序组织的计算机数据和指令的集合。
>
>数据是用于描述事物的信息。

>指令就是告诉计算机如何处理数据
### 软件开发的步骤
1. 需求分析 
2. 设计
 - 概要设计
 - 详细设计 
3. 编码
4. 测试：单元测试（黑盒测试、白盒测试）
5. 上线：试运行、正式交付

### 常用的DOS命令###
dos命令含义

- cd 目录：  将当前目录修改为指定的目录
- cd..：退回到上一级目录
- cd\：退回到根目录
- 盘符：  切换为当前的盘符
- cls：将当前命令行屏幕内容清空
- dir:列出当前目录下的文件及文件夹
- md：创建目录
- rd： 删除目录
- del:删除文件
- exit：退出dos命令行

### java简介###
>Java语言是SUN公司(Sranford University NetWork,斯坦福大学网络公司)1995年推出的一门高级编程语言。PS：被Oracle公司收购


**java语言的含义：**面向对象的编程语言。

java包括：

- javase：Java2 Platform Enterprise Edition 企业版
- javaee: Java2 Platform Standerd Edition 标准版
- javame: Java2 Platform Micro Edition 小型版

java的版本：jdk 1.0、jdk 1.5、jdk 1.7

java语言的特点：

- 安全的
- 跨平台：java在不同的操作系统中都能运行。原理：在需要运行java应用程序的操作系统中安装java虚拟机(jvm:java virtual machine)，由jvm来调用操作系统底层指令解析、执行java程序，从而在改操作系统中运行。（jvm不是跨平台的，不同的操作系统有不同版本的jvm）
- 健壮
- 开源、免费
## JAVA环境的搭建##
- 环境变量：供os使用的变量。
### 安装JDK	
### 设置环境变量
- 环境变量的划分：
 - 用户变量
 - 系统变量
- 配置环境变量的目的何在？
>为了在任意盘符任意目录下调用环境变量值指定目录下的所有可执行文件。
### 什么是JRE，什么是JDK
>jre:java runtime enviroment:java运行环境:包括java虚拟机和java程序所需的核心类库等，如果要运行一个开发好的java程序，计算机中只需安装jre即可。

>jdk:java Development Kit:java开发工具包：提供给java开发人员使用的，其中包含了java的开发工具，也包括了jre.所以安装了jdk，就不用再单独安装jre了，其中的开发工具有编译工具（javac.exe），打包工具（jar.exe）等。

>**为什么jdk中包含一个jre呢？**其一：开发完的程序，需要运行一下看看效果，就像exe文件需要在windows环境下运行一样。其二：也是最重要的，jdk中的开发工具（如javac.exe、java.exe等）其实都是java语言编写的应用程序，为了方便使用才打包成exe文件，如果没有jre那么这些工具是运行不了的。

>**总结：**jre：jvm+类库（java librery）。jdk：jre+java的开发工具。
## 第一个Java程序HelloWord	
### 在记事本编写程序
	在记事本编写程序:创建一个后缀为.java的源程序，编写代码。
### 在DOS命令窗口编译执行	
>在DOS命令窗口编译执行:
>
> javac java源程序名.java   <--编译生成字节码文件，也就是可执行的java程序。 
> 
> java  编译生成的字节码文件名(.class)
### 讲解源程序的编译执行
**java源程序的构成** 

    如何编译一个带了包名的java源程序?
     javac -d .  源程序名
     含义：在当前目录下创建以包名命名的目录，层次结构与包名的顺序一致，将生成的字节码文件置于最内层目录。

    如何运行一个带了包名的字节码文件？
     1）退到最外层目录
     2）通过java 类的全限定名
        类的全限定名：带了包名的类名，如： com.l000phone.day01.JavaCodeIncludeDemo

    源程序的构成：1）包 2）导入语句 3)类（主方法：程序执行的入口）

**注释:**

	当行:语法：//xxx
	多行:语法：/*  xxxx  */
	文档:/**
	     xxxxx
	     */
	    文档注释的作用：生成程序使用说明书。
	    注释的作用：用来给程序员阅读代码看的。
**classpath环境变量的设置以及环境变量的临时设置**
	
	classpath的作用：类似于path环境变量，高数jvm去哪里找class文件。
	
	jvm查找类文件的顺序：
  	1.如果没有配置classpath环境变量，jvm只在当前目录下查找要运行的类文件。
	2、如果配置了classpath环境变量，jvm会先在classpath环境变量值得目录中查找要运行的类文件。
	classpath环境变量配置语法？
	 环境变量名：classpath
	 环境变量值：字节码文件所在的目录，值一般是jar文件
	 达到效果:在任意的盘符，任意的目录下可以运行classpath环境变量值该目录下所有的字节码文件。
	
	环境变量的临时设置:
	语法： set 环境变量名=环境变量值
	如何查看环境变量的值？
	语法： set 环境变量名
	特点：只针对当前命令行窗口有效，关闭之后，失效。

**java源程序深度剖析：**

- 一个源文件可以书写n个类，只有一个类可以使用public修饰符来修饰，此时，该类的类名必须与源程序名保持一致。
- 每个类都可以有入口（main方法）
- 一个源程序中有几个类，编译后，就会生成几个字节码文件。

## 关键字、标识符	
### 什么是标识符	
>标识符：由字母、数字、下划线、**$符合**组合而成的字符序列。
### 标识符的命名规范	
>合法的java标识符的命名规范：
>不能以数字开头
>- 不能是关键字、保留字
>
>推荐合法的标识符的写法：
>1、遵循“驼峰命名法”
>   a)类名、接口名
>   b)变量名、参数名、方法名
>2、长度<= 15 如：stuName
>3、见名知意
>
>合法的标识符的用途何在？
>类名、接口名、包名、方法名、参数名、变量名等等。
> 
>包名的命名规范：1）所有包小写，各个包之间以.隔开。
>              2）客户公司网站名的倒写，除去“www”   www.lenovo.com-->com.lenovo.项目名的缩>写.模块名
### JAVA关键字	
JAVA关键字：java语言的设计者赋予了特殊含义的英文单词。如：class int public package import 等等
保留字：预备关键字  如：goto byValue, cast, false, future, generic, inner, operator, outer, rest, true, var const,null
## 常量、进制转换	
### 什么是常量	
    固定不变的量
    语法：final 类型 标识符;
### 二进制转换十进制
    特点：
    1、常量只能赋值一次。
    2、赋值的方式分类：
        ①声明的同时直接赋值；
        ②声明与赋予初始值分开。
**常量的命名规范**：

    全部大写，遇到不同的单词，使用下划线分割。如：final CIRCLE_PI = 3.1415;
    二进制转换十进制: 从低位开始，对应的基数乘以进制的n次幂之和。（n：0～n-1）.
    如：101 --》1*2（0） + 0*2（1） + 1*2（2） = 5
**算法：**

    软件前辈们总结出来解决某类问题最有效的方法。
**十进制转换成二进制：**

    要转换的数对进制求余数，然后，对应的商继续对进制求余，直到商为0。
    然后，将所有的余数从下往上拼接起来，即为最终的结果。
## 变量、数据类型、类型转换	
### 什么是变量	
>用来存储变动的值的合法的java标识符。
>
>类型 变量名;
>
>特点：1、局部变量使用之前一定要进行初始化。2、变量的值是变动的。
### 变量的声明和初始化	
### 变量的分类	及作用域
	①全局变量：定义在类体中，与方法平级的变量。
	     特点：a）作用域范围在整个类体中都有效。
	          b）全局变量有分为：
	               非静态的全局变量，只能使用在普通方法中，静态方法不能使用。
	               静态的全局变量，既可以使用在普通方法中，可以使用在静态方法中。
	          c)全局变量都有一个默认的初始值。
	②局部变量：定义在方法体中或者是代码块中的变量。
	   	 特点：a）作用域范围仅仅限于其所在的方法体或者是代码块。
	          b）子块可以访问其之前父块中的变量，反之，父块不能使用子块特有的变量。
	          c)局部变量使用之前一定要进行初始化
## 变量名的命名规范	
### JAVA中的数据类型	
### 基本数据类型	
- 数值型（整型<byte,short,int,long>
- 浮点型<float,double>）
- 字符型(char)
- 布尔型(boolean)。
### 引用数据类型	
- 类
- 接口
- **数组**
## 数据类型的取值范围
			类型名	关键字	占用的字节数	取值范围		规律
	整型     字节	byte	1			-128～127   
			短整型	short	2					-2（15）～　2（15）-1   
			整型	int		4						2（31）～　2（31）-1 
			长整型	long	8					-2（63）～　2（63）-1 
    
	浮点型	  单精度	float	4
			双精度	double	8 
			字符型	char	2 			0～65535
			布尔型	boolean	1 			false,true
**java中数据类型特别说明：**

- 浮点型在计算机底层，使用的是科学计数法来进行存储的。
  1234.89--》1.23489*10(3)(书本上)--》1.23489E3
  **注意：**
 - 小数默认是double型
 - 显示的结果分为：①数据小，显示本身的数值 ②数据大，显示科学计数法的结果。
 - 浮点数在底层，使用近似值来表示，不适用于在银行、电信领域存储金额。（使用BigDecimal来存储小数，小数点后精确到的位数可以任意定制）
 - 同字节的浮点数较之于同字节的整数或者是比起字节数多的整数，能够存储更多更大的数值。
- 字符型说明：
>*语法*：char 变量 = '单个字符';
>
>*特点*：1、单个字符:个数是1，包括：汉字，英文字母，特殊的符号；
2、不允许什么都没有3、采用的字符集是unicode,该字符集的特点是：任何字符都占用2个字节。
>
>*字符集*：将语言文字与计算机底层表示的数值建立映射关系的二维表。('\U0000'～'\UFFFF')
>
>常用的字符集：ascii:a～ｚ　A～Z 0～9 'a'-->97 'A'-->65 '0'-->48
 
- gb2312:简体中文
- gbk:简体、繁体中文
- gb18030:简体、繁体、生僻的汉字
- iso-8859-1:西文字符集

## 数据类型的转换	
### 自动类型转换	
>相兼容的数据类型之间，将取值范围较小的数值和变量赋值给取值范围较大的类型对应的变量。

如：`int age = 34;long age2 = age;//long age2 = 45;`

**注意：**

- byte、short、char类型的变量进行运算时，首先会自动提升为int型，然后，参与运算。
- 整型数值默认的类型是int。
- 单精度的浮点数进行运算时，不会自动提升为double。
### 强制类型转换
>相兼容的数据类型之间，将取值范围较大的数值和变量赋值给取值范围较小的类型对应的变量。
>
>语法：取值范围较小的类型  变量名 = (取值范围较小的类型)取值范围较大的变量或者是值;如：`int age = 56;byte ageTmp = (byte)age;`
## 运算符	
### 常用运算符八类
- 算术运算符：`= - * / %  ++ --`
- 赋值运算符：`= -= += *= /= %=`	
- 关系运算符：`> >= < <= !=`
- 逻辑运算符：`&    | ^  !  &&  ||`	
- 三目运算符：`与值相同的类型 变量名=取值为boolean型的表达式?值1：值2;`	
- 字符串连接运算符：`+`	
- 位运算符：`& | ^ ~	`
- 移位运算符：`<< >> >>>`	
## 表达式的值和类型
表达式：由操作符和操作数组合而成的式子。如：int a = b+3;
## 转义字符
>使用在字符串中，用来定制字符串输出格式的特殊符号。

### 常用的转义字符介绍

	\t                  一个退格键
	\n                  换行（linux,unix）
	\r                  回车(window中的换行\r\n)
	\'                  将'当成一个普通的字符显示出来('-->特殊含义：字符值)
    \"                  将"当成一个普通的字符显示出来（"-->特殊含义：字符串的值）
    \u                  unicode码值
    \\                  将\当成一个普通的字符显示出来(\-->特殊含义：指定一个字符属于转义字符)

案例：使用一个打印语句，将字符串“哥们，最近还好吗？有没有看"刚囧"？'男'主角啥名？”以下述格式输出

哥们，
最近还好吗？	有没有看
"刚囧"？'男'\主角啥名？`
## 运算符优先级
- 先乘除，后加减；
- 若需要提升优先级，添加括号；
- .，++，--运算符的优先级很高，赋值运算符的优先级最低的。