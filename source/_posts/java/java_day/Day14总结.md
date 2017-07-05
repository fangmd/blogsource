---
title: Day14 总结
date: 2015-12-28 21:18:12
tags: [java,day]
categories: java基础

---

# Day14 总结
## File类	
>File类：对存储在磁盘上的文件和目录的抽取和封装。
### File类作用	
>常用的字段：`static String separator `：会根据底层平台，动态生成一个分隔符（目录之间的分隔符）windows -->\ ;linux --> /
### File类的构造方法	
>`File(File parent, String child) `：根据父目录对应的文件对象和该目录下的文件
>
>`File(String pathname)` ：将参数指定的目录封装成一个file对象（绝对路径和相对路径）
>
>`File(String parent, String child)` ：根据父目录对应的路径，和该目录下的文件或目录
xxx
### File类文件属性方法	
### 使用File类对文件进行操作	
### 使用File类浏览目录中的文件和子目录	
## IO概念和种类:	
### 什么是IO流？	
>IO流：磁盘上的文件与内存进行数据传递的管道（通路、媒介）。
###流的作用和原理？	
## IO流的种类:	
### 输入流、输出流	(**相对于内存而言**)
### 字符流、字节流	
### 节点流、处理流
1. **节点流：**与具体的存储介质直接进行交互的流。（API中涉及到的类：FileInputStream -->文件字节输入流，FileOutputStream-->文件字节输出流FileReader-->文件字符输入流，FileWriter-->文件字符输出流）
2. **处理流：**不与具体的存储介质直接进行交互的流。（API中涉及到的类：	
	1. BufferedInputStream -->带缓存的字节输入流，
	2. BufferedOutputStream-->带缓存的字节输出流；                 3. BufferedReader -->带缓存的字符输入流
	4. BufferedWriter -->带缓存的字符输出流）
	
##字节输入流:	
###InputStream类的常用方法	
1. int available() ：返回的是流中所有数据的长度（单位：字节）。
2. void close()：关闭此输入流并释放与该流关联的所有系统资源。 

###InputStream类的子类:文件输入流FileInputStream	
###FileInputStream构造方法和常用方法	
##字节输出流:	
###OutputStream类的常用方法
1. void close() 关闭此输出流并释放与此流有关的所有系统资源。 
2. void flush() 将输出流中的数据刷新。
3. void write(byte[] b)：将参数指定的字节数组写入到输出流中。
4. void write(byte[] b, int off, int len) ：将参数指定的字节数组的一部分写入到输出流中。
5. abstract  void write(int b) ：将参数指定的字节写入到字节输出流中。

    需求：使用InputStream,OutputStream,实现文件的拷贝。将指定目录下的文件，拷贝到另外一个目录中去。
    
    思路：
    1、构建文件字节输入流对象（理解：建立了一条磁盘文件到内存的通路）
    2、构建文件字节输出流对象（理解：建立了一条内存到磁盘存储介质的通路）
    3、通过循环从输入流中读取数据，与此同时，将数据写入到输出流中，直到数据处理完毕。
    4、释放资源
    
    字节流的适用场景：
       拷贝具有特定格式的源文件（声音、图像、视频、压缩包、doc、excel、exe文件表格等等）时，要使用字节流，
    不会破坏源的格式，否则，拷贝后，文件无法打开、运行等等。

    路径：
     绝对路径：从盘符开始的路径。  如：c:/temp.txt 
     相对路径：不从盘符开始的路径。如： ../../tmp/test.txt
    
    目录和目录、目录和文件之间的分隔符：
    windows:  c:\\temp\\test.txt
    linux :   temp/test.txt
    
    File.seperator; --->根据系统运行的平台，动态生成一个分隔符
###OuputStream类的子类:文件输出类FileOutputStream	
###FileOutputStream构造方法和常用方法	
##字符输入流:	
###Reader类的常用方法	
###Reader类的子类:FileReader	
###FileReader构造方法和常用方法	
##字符输出流	
###Writer类的常用方法	
1. Writer append(char c) ：将字符追加到字符输出流的末尾。
2. Writer append(CharSequence csq) ：将字符追加到字符输出流的末尾。
3. void write(char[] cbuf) ：将参数指定的字符数组中的数据写入到字符输出流中。(字符流中的read(char[]cbuf)，作用是：将流中的数据读取出来填充字符数组)
4. void write(String str) ：直接将字符串写入到字符输出流中。
###Writer类的子类:FileWriter	

    需求：使用Writer，将内存中的数据写入到磁盘一个文件中。
    
    案例：使用字符流，实现文件的拷贝。
    
    字符流的适用场景：操作文本文件时，建议使用。若操作一些带有特殊格式（声音、图像、视频、压缩包等等）的文件，不要使用。

###FileWriter构造方法和常用方法
