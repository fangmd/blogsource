---
title: String
date: 2016-03-25 21:18:12
tags: java基础
category: java基础
---

## String StringBuffer StringBuilder
### String类的特点
1. 字符串对象一旦被初始化就不会被改变；（只会创建新的字符串对象）
    >案例1：
    >
    >      String s1 = "abc";

	>      String s2 = "abc";

	>      s1 == s2; 为true
	>原因分析：这种方法创建字符串的时候，会先从字符串常量池总查找有没有一样的对象，如果有就不会创建新的对象而是直接指向这个对象，所以s1和s2指向同一个对象；

    >案例2：
    >
    >      String s1 = "abc";

	>      String s2 = new String("abc");

	>      s1 == s2; 为false
	>原因分析：s1创建后，在字符串常量池中创建了“abc”对象，而s2是在堆内存中创建了另外一个“abc”字符串对象，所以两个不是同一个对象；

<!--more-->

2. String的构造方法：
	1. 无参构造方法；
	2. 使用字节数组构造；
	3. 字节数组和指定的编码集（Charset）
	4. 字节数组的子集；
	5. 字节数组的子集和指定的编码集；
	6. 字符数组构造；
	7. 字符数组的子集；
	8. 以String对象为参数，创建副本；
	9. 以StringBuffer或者StringBuilder对象为参数；

3. String普通方法：
	1. 获取：
		1. 获取字符串长度：`int length();`
		2. 根据位置获取字符：`char charAt(int index);`(注意这里获取的是Char类型不用使用equals和Stirng类型进行比较)
			1. Character.toString(s2.charAt(begin))
			2. new Character(s2.charAt(begin)).toString()
			3. (s2.charAt(begin)).toString()：报错，这里不能自动装箱
		3. 根据字符获取在字符串中的位置：`int indexOf(int ch);`
		4. 从指定位置开始查找ch第一次出现的位置：`int indexOf(int ch,int fromIndex); int indexOf(String str); int indexOf(String str,int fromIndex);`
		5. 根据字符串获取在字符串中最后一次出现的位置：`int lastIndexOf(int ch); int lastIndexOf(int ch,int fromIndex); int lastIndexOf(String str); int lastIndexOf(String str,int fromIndex);`
		6. ps.如果不存在就会返回-1；
		6. 获取字符串的子串：`String substring(int beginIndex,int endIndex); String substring(int beginIdex);`
	2. 转换：
		1. 字符串转换成字符串数组（字符串切割）：`String[] split(String regex);`（正则表达式"\\."点在正则表达式里是特殊符号，\也是特殊符号也需要转义）
		2. 字符串变成字符数组：`char[] toCharArray();`
		3. 字符串变成字节数组：`char[] getBytes();`
		4. 字符串中字母大小写转换：`String toUpperCase(); String toLowerCase();`
		5. 字符串中内容替换：`String replace(char oldChar,char newChar); String replace(String s1,String s2);`(如果没有找到要替换的内容，则返回原字符串)注意：这个方法返回值是Stirng，需要用原引用接收或者用其他引用接收，不接收改变后的对象就无法使用；
		6. 去除两端空格：`String trim();`
		7. 将字符串连接：`String concat(String str);`(concat效果与“+”效果一致，但是效率更高些)
		8. 其他类型转换成字符串：`static String valueOf(....)`(也可以传对象，返回对象的字符串表现形式)

	3. 判断：
		1. 两个字符串的内容是否一样：（String类重写了toString方法）`boolean equals(Object obj); boolean equalIgnoreCase(String str);`
		2. 字符串中是否包含指定的字符串：`boolean contains(String str);`
		3. 字符串是否以指定字符串开头，是否以指定的字符串结尾：`boolean startWith(String str); boolean endsWith(String str);`

	4. 比较：`int compareTo(String str);`0,>0,<0
	5. 返回字符串对象的规范化表示形式：`String intern()`当调用这个方法的时候，如果池中已经有一个等于此String对象的字符串，则返回池中的字符串。否则，将此Stirng对象添加到池中，并返回String对象的引用。
	
			String s1 = "abc";
			String s2 = new String("abc");
			String s3 = s2.intern();
			String s4 = new String("abcd");
			String s42 = s4.intern();
		
			System.out.println(s1==s2);//false
			System.out.println(s1==s3);//ture
			System.out.println(s2==s3);//false
			System.out.println(s42==s4);//false
### StirngBuffer  
>字符串缓冲区，用于存储数据的容器

特点：

1. 长度可变；
2. 可以存储不同类型的数据；
3. 最终要转成字符串进行使用；

StringBuffer：字符串缓冲区初始容量为16个字符，其实质还是数组。

方法：

1. 添加：`StirngBuffer append(data); StringBuffer insert(index,data);`
2. 删除：`StringBuffer delete(int start,int end); StringBuffer deleteCharAt(int index);`	
3. 查找：`char charAt(int index); int indexOf(Stirng str); int lastIndexOf(String str);`
4. 修改：`StringBuffer replace(int start,int end,String str); void setCharAt(int index,char ch);`
5. 其他：`void setLength(int newLength); StringBuffer reverse();`
	
备注：

1. 使用setLength设置StringBuffer中字符序列的长度时，如果小于已有的长度，就会清除缓冲区中的一部分内容，如果大于，就会扩充缓冲区用空格字符填充；
2. 当创建的StirngBuffer内容长度大于16，就会创建新的数组，长度比旧数组要长。然后把旧数组的内容拷贝到新的数组，超出旧数组长度范围的内容就会放在新数组现在内容的后面，也可以用过StringBuffer(int capacity);够着函数自己设置长度；

### StringBuilder
>JDK1.5后出现的

不同点：

1. 线程不同步，适用于单线程，效率高；
2. 不安全；
	
 