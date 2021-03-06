---
title: java -- day10
date: 2015-12-28 21:18:12
tags: [java,day]
categories: java基础

---


# Day10
## 异常机制
### 异常的概念
>程序在运行或者编译的时候出现的不正常的现象。	
### 异常的分类	
Thorwsable:异常的根类，直接继承至`Object`

两个子类：

1. `Error`程序不能处理的jvm级别的异常。如：`StackOverflowError`
2. `Exception:`程序能够捕获或者处理的异常。
	1. `RuntimeException：`运行时异常：程序在运行过程中出现的异常
	2. `Checked：`：程序在编译时的错误，且必须处理的异常。

常见的`RuntimeException`异常：

1. `ArithmeticException：`算术异常，例子：两个整数相除，除数为0；
2. `InputMismaticException:`输入不匹配异常，例子：`Scanner`扫描器对象试图从控制台获得一个与该对象取出的数据不吻合如：`input.next();`
3. `NullPointerException:`空指针异常，例子：一个引用类型的实例为`null`，调用方法。如：`String str = null; boolean isTrue = str.equals("")`;
4. `ArrayIndexOutOfBoundsException:`数据下标越界异常，例子：访问数组中的元素，下标>=数组的长度。
5. `NumberFormatException:`数据格式化异常，例子：将一个非数字的字符串转换成数据包装类型，如：`new Integer("abs")`
6. `ClassCastException：`强制类型转换异常，例子：将一个父类的引用强制转换为没有继承关系的类型时；

异常的特点

1. 若某行的代码在执行过程中抛出异常，那么后续的语句都不会执行；
2. 若异常已经被处理，那么后续的语句会继续执行；

常见的`Checked`异常：

1. `ClassNotFoundException:`找不到类的异常，例子：通过`Class.forName("类的全限定名对应的字符串")`如：`Class.forName("a.b.bc.Dog");`
2. `ParseaException:`日期解析异常，将一个与定制格式不匹配的日期字符串解析为Date类型对象；
3. `DataFormatException:`

**如何鉴别异常的类型：**

1. `RuntimeException:`
	1. 编译能通过
	2. 查看该异常类型的层次结构图，该异常直接或者间接继承与`RuntimeException`
2. `Checked：`
	1. 若不处理，编译报错
	2. 查看该异常的类型层次结构图
### 异常的处理方式	
①try{
    //可能产生异常的代码
  }catch(异常类型 e){//捕获
      //处理措施（补救方案）
  }
  
适用场景：可能出现异常的情形只有一个（很多--》捕获的异常类型是Exception,可以接收任意子类型异常对象），处理方式一样。


②****使用得最外广泛

    try{
    //可能产生异常的代码
      }catch(异常类型1 e){//捕获
      //处理措施（补救方案）
      }catch(异常类型2 e){//捕获
      //处理措施（补救方案）
      }
      ...
      catch(异常类型n e){//捕获
      //处理措施（补救方案）
      }
    
**注意点**：

1、若异常类型之间有继承关系，那么，子异常类型的捕获必须置于父类型异常之前。
2、若异常类型之间没有继承关系，那么，与顺序无关。

适用场景：异常较多，且每种类型的异常处理方式是不一样的。

③****使用得最外广泛

	 try{
	//可能产生异常的代码
	  }catch(异常类型1 e){//捕获
	  //处理措施（补救方案）
	  }catch(异常类型2 e){//捕获
	  //处理措施（补救方案）
	  }
	  ...
	  catch(异常类型n e){//捕获
	  //处理措施（补救方案）
	  }finally{
	 //无论异常发生与否，都会执行的代码
	 //---->在真实项目中，一般书写资源释放的语句（如：io的关闭）
	  }

注意：
1、finally语句块一般都会执行，但是例外的情况：
  ①若jvm异常退出  System.exit(负数);
  ②jvm正常退出    System.exit(正常);
    --》无条件终结jvm的执行
2、fianlly语句块中对于之前书写的return 变量值是没有影响的。

适用场景：异常较多，且每种类型的异常处理方式是不一样的,且要单独进行资源释放。（io流）

④   

	 try{
    }finally{
    }
使用场景：所有异常的处理，以及资源释放的语法都置于finally语句块中。	
##### 抛出异常：throws和throw	
1. throws：
	1. 使用在方法的声明部分，表示抛出本方法中可能产生的所有异常类型
	2.  语法：访问权限修饰符 返回值类型 方法名（形参列表） throws 异常类型1，异常类型2.。。。{方法体}
	3.  从设计者角度，在设计方法的时候若可能发生异常，而且处理的时机不成熟，就需要抛给能够处理的调用者，若该调用者处理的时机还不成熟，继续向上抛，直到能处理为止（到main中不能抛必须处理） 时机：若方法有参数，参数值从外部传入，此时就不成熟。
2. throw：
	1. 使用在方法同中，后接一个定制了异常信息的异常对象。
	2. 语法：访问权限修饰符 返回值类型 方法名（形参列表） throws 异常对象所对应的异常{方法体 throw 异常对象；}
	3. 语法  把 throws 去掉
	4. 从设计者角度，设计throw是为了便于定制异常信息。

throw 和 throws 是否同时出现：不用。

根据throw后异常对象所属的类型来确定。前提：方法体中处理异常的时机不成熟，而且，需要对异常信息进行人性化定制的场合。
1. 必须出现：throw后异常对象属于编译时异常
2. 不用出现，throw后异常对象属于运行时异常。
#### 自定义异常

自定义异常:jdk中设计好的异常类型，不能满足现实的需求，定制的异常类型。

自定义异常的书写方式：

第一种：继承RuntimeException
   不需要立即进行处理的异常。 --》运行时时异常 

第二种：继承Exception
   需要立即进行处理的异常，否则编译报错。 --》编译时异常 

需求：家具厂生产桌子，桌子的半径若在范围[30.567,31.889]cm区间之内，就是
合格的产品，否则，就是残次品，次品需要返工的。模拟场景：木匠师傅搬着刚刚精心制作完成的桌子，去质检部检测，检查合格，师傅休息；不合格，让其返工，直到合格为止。
