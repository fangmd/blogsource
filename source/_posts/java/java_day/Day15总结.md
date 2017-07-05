---
title: Day15 总结
date: 2015-12-28 21:18:12
tags: [java,day]
categories: java基础

---

# Day15 总结
## 标准输入输出流	
### 使用System.in获取键盘数据	
>标准输入流System.in,类型是InputStream,只有将该流的对象作为System类的静态属性时。（静态最终属性）
>
含义：获取用户从控制台录入的数据，将其通过InputStream字节输入流读如到内存中。
例如：`Scanner input = new Scanner(System.in);`
### 使用System.out输出数据	
>标准输出流System.out,类型是PrintStream，只有将该流的对象作为System类的静态属性时。
>
含义：将内存中的数据写入到打印字节输出流中，然后，输出到控制台。（默认的目的地）

两个重要的`System`静态方法：

1. static void setIn(InputStream in) ：定制标准输入流的源。（默认源是：控制台）
2. static void setOut(PrintStream out) ：定制标准输出流的目的地。（默认目的地是：控制台）
##转换流	
###InputStreamReader的构造方法	
`InputStreamReader`-->将字节流转换成字符流（设计者说：`InputStreamReader` 是字节流通向字符流的桥梁）

>`InputStreamReader(InputStream in)` ：将参数指定的一个字节输入流封装成一个字节字符转换流。
>
`InputStreamReader(InputStream in, Charset cs)`：将参数指定的一个字节输入流以指定的字符集封装成一个字节字符转换流。

>`InputStreamReader(InputStream in, String charsetName) `：xxx
###InputStreamReader的使用	
>`String getEncoding()` ：获得转换流使用的字符集名称。

InputStreamReader设计的本质：

1. 操作文本文件时，将字节流转换成字符流，便于操作。
2. 可以解决字符集乱码的问题：
	1. 若是字节输入流，读取文本文件时，文本文件中若存在中文，字节数组的长度很小的场合（比如1），所有的汉字都变成了乱码！若转换成字符流，就没有乱码了！
	2. 若是源的字符集与默认的字符集不吻合，也有乱码。解决方案：读取源时，指定与之匹配的字符集，保证读取到流中的数据没有乱码的。

###OutputStreamWriter的构造方法	
>OutputStreamWriter--》将字符流转换成字节流输出到存储介质上去（设计者说：OutputStreamWriter 是字符流通向字节流的桥梁）

>`OutputStreamWriter(OutputStream out)` ：通过参数指定的字节流构建字符流的对象
>
>`OutputStreamWriter(OutputStream out, Charset cs)` ：xxx
>
>`OutputStreamWriter(OutputStream out, String charsetName)`:创建使用指定字符集的 OutputStreamWriter。
###OutputStreamWriter的使用	
>String getEncoding()  返回此流使用的字符编码的名称。

    需求：
    1、使用字节字符转换输入流，结合字符字节转换输出流，实现文件的拷贝。
    
    要求：①源文件的字符集是UTF-8
         ②目标文件的字符集是GBK
    
    --------------------------------
    要求：①源文件的字符集是GBK
         ②目标文件的字符集是UTF-8
     
    --------------------------------
    要求：①源文件的字符集是UTF-8
         ②目标文件的字符集是UTF-8

思路：

1. 通过`FileInputStream`的对象构建`InputStreamReader`的对象
2. 通过`FileOutputStream`的对象构建`OutputStreamWriter`的实例
3. 循环读写
4. 释放资源
###将键盘数据转成字符流写入文本	
##字节缓存流	
>在操作数据时，先将数据读入内部的缓冲区（字节数组），然后，程序操作的是缓冲区中的数据，最大的优点：可以提高数据在流中传输的速度。在项目中建议多使用。
###字节缓存流的构造方法	
>BufferedInputStream(InputStream in) xx

>BufferedOutputStream(OutputStream out) 
###使用字节缓存流对文件进行读写	
>BufferedInputStream 为另一个输入流添加一些功能，即缓冲输入以及支持 mark 和 reset 方法的能力

**常用方法：**

1. void mark(int readlimit) ：在流中相应的位置添加一个标记。
2. boolean markSupported() ：判断此种类型的流是否支持添加标记
3. void reset()  ：让流出数据回滚到流中，实现：覆水可收的效果。

    需求：
    使用带缓存的字节流实现：将用户从控制台录入的数据，写入到磁盘上另外一个文件中，与此同时，将用户录入的数据显示到控制台上。
    直到用户录入886，就退出程序。
    
    思路：

    1. 通过标准输入流Sytem.in构建缓冲字节输入流
    2. 通过定制标准输出流的目的地，封装一个打印输出流，据此，构建一个缓存字节输出流
    3. 循环读取用户从控制台的录入，写入到缓存字节输出流中，同时，打印到控制台上，直至886
##字符缓存流	
>流中的数据先存储在内部的缓冲区中（字符数组），然后程序与该缓冲区中的数据进行交互。可以提高数据在流中传输的效率。

>官方对BufferedReader的说明：从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。 
###字符缓存流构造方法	
>BufferedReader(Reader in) 

>BufferedWriter(Writer out) 
###字符缓存流的常用方法:readLine()，  newLine()  	
>String readLine() ：从流中读取一行数据（注意：此处换行符读取不进来的）

>void newLine() 写入一个行分隔符。（就是换行符，根据底层os的不同，动态生成与之匹配的换行符） 
###字符缓存流对文件进行读写

    需求：使用缓冲字符输入、输出流，实现文本文件的拷贝。
    要求：设计一个工具类，设计一个拷贝的工具方法。
    验证：①拷贝文本文件，ok
          ②拷贝可执行文件，如：*.exe,执行不了的。

    思路：
    1、通过FileReader构建BufferedReader的对象
    2、通过FileWriter构建BufferedWriter的对象
    3、循环读写
      分别使用两种方式：传统方式（借助字符数组）
       新潮方式（借助：readLine()，newLine()）
    xxx 
    
    --->拓展：
    需求：使用缓冲字符输入、输出流，实现文本文件的拷贝。
    要求：源的字符集是UTF-8,目的地的字符集是UTF-8.
###Properties资源文件操作类与io流如何结合

结合步骤：

①在项目中相应目录下准备资源文件（.properties）
 文件中以键值对的形式，将项目中一些共通的信息记录起来

②设计工具类，工具方法
  使用将资源文件装载进内存，具体装载到Properties实例中，
  从实例中读取配置信息
