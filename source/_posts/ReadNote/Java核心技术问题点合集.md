---
title: Java核心技术回顾
date: 2016-01-07 21:38:14
tags: java基础
categories: 读书笔记

---


## Java核心技术回顾 30 - 75
### P43：基本数据类型在计算时的转换问题：

- 如果两个操作数总有一个是double ，则另一个操作数转成double
- 否则，如果一个操作数是float，则另一个操作数转成float
- 否则，如果一个操作数是long，则另一个操作数转成long
- 否则，两个操作数转成int类型。

<!--more-->

	在没有double float long时数据转成int计算，在有这些数据类型时，优先级为：double>float>long

	如何查看一个数据的类型：
		
		class Demo
		{
			public static void main(String[] args)
			{
				int i;
				System.out.pritnln(getType(i));
			}
			public static getType(Object o)
			{
				return o.getClass().toString();
			}	
		}

### P44 &&运算符的优先级比||高
验证code：
	
### P46拼接 + **String类**
任何Java对象都有可以转换成字符串，将第一个字符串与一个非字符串拼接时后者会转换成字符串。
	
	int age=13;
	String rating = "PG" + age; 
	rating 为 "PG13“
	这种特性一般用在输出语句中。
	System.out.println("the"+任何数据类型);

将substring方法和拼接符一起使用可以对字符串进行修改。

String类称为**不可变字符串**，不过可以修改字符串变量让它引用另一个字符串。**优点：**让字符串共享。
### P49 代码点与代码单元
>char类型是一个采用UTF-16编码表示Unicode代码点的代码单元。大多数Unicode字符使用一个代码单元就可以表示了，而**辅助字符**需要一对代码单元表示。

char --> 代码单元:用length获得

理解代码点和代码单元

	import java.io.UnsupportedEncodingException;

	public class CharactersetTest {
	public static void main(String[] args) throws UnsupportedEncodingException {
		String str = "中";
		int codepoint = str.codePointAt(0);

		System.out.println("'" + str + "'的UNICODE编号(编码点,Unicode code point)=" + codepoint + "[0x"
				+ Integer.toHexString(codepoint) + "]");
		System.out.println();

		byte[] bytes = str.getBytes("utf32");
		System.out.println("'中'的UTF-32编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("utf16");
		System.out.println("'中'的UTF-16编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("UTF-16BE");
		System.out.println("'中'的UTF-16BE编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("UTF-16LE");
		System.out.println("'中'的UTF-16LE编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("utf8");
		System.out.println("'中'的UTF-8编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		byte b = bytes[0];
		System.out.println("3字节UTF8的第1个字节:" + toBinaryString(b) + "[1110xxxx]");
		b = bytes[1];
		System.out.println("3字节UTF8的第2个字节:" + toBinaryString(b) + "[10xxxxxx]");
		b = bytes[2];
		System.out.println("3字节UTF8的第3个字节:" + toBinaryString(b) + "[10xxxxxx]");
		bytes = str.getBytes("GBK");
		System.out.println("'中'的gbk编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("GB2312");
		System.out.println("'中'的GB2312编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("US-ASCII");
		System.out.println("'中'的US-ASCII编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("ISO-8859-1");
		System.out.println("'中'的ISO-8859-1编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes) + ",可以看出被丢了一半");
		System.out.println();

		str = "A";
		bytes = str.getBytes("utf32");
		System.out.println("'A'的UTF-32编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("utf16");
		System.out.println("'A'的UTF-16编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("UTF-16BE");
		System.out.println("'A'的UTF-16BE编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("UTF-16LE");
		System.out.println("'A'的UTF-16LE编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("utf8");
		System.out.println("'A'的UTF-8编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("GBK");
		System.out.println("'A'的gbk编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));
		bytes = str.getBytes("GB2312");
		System.out.println("'A'的GB2312编码单元长度为：" + bytes.length + ",内容为：" + toHexString(bytes));

		System.out.println();

		byte[] bytes2 = { 0x4E, 0x2D };
		String str2 = new String(bytes2, "UTF-16");
		System.out.println("从0x4E2D 按UTF-16 转出来的：" + str2);

		byte[] bytes4 = { (byte) 0xfe, (byte) 0xff, 0x4E, 0x2D };
		String str4 = new String(bytes4, "UTF-16");
		System.out.println("从0xFEFF4E2D 按UTF-16 转出来的：" + str4);

		byte[] bytes3 = { (byte) 0xE4, (byte) 0xB8, (byte) 0xAD };
		String str3 = new String(bytes3, "UTF8");
		System.out.println("从0xE4B8AD 按UTF-8 转出来的：" + str3);

	}

	public static String toBinaryString(byte b) {
		StringBuilder sb = new StringBuilder("");
		String temp = Integer.toBinaryString(b & 0xff);
		sb.append("00000000".substring(temp.length())).append(temp);
		return sb.toString();
	}

	public static String toHexString(byte[] bytes) {
		StringBuilder sb = new StringBuilder("0x");
		for (int i = 0; i < bytes.length; i++) {
			String temp = Integer.toHexString(bytes[i] & 0xff);
			sb.append((temp.length() == 1) ? "0" + temp : temp);
		}
		return sb.toString();
	}
}
### P55 控制台密码输入 Console类
	Console cons = System.console();
	String username = cons.raedLine("User name:");
	char[] passwd = cons.readPassword("Password:");

-
  
	public class Main {    
   		 public static void main(String[] args){  
       		 Console console = System.console();  
       		 String password ;  
       		 password = new String(console.readPassword());  
      		 System.out.println("password="+password);  
    	}  
	} 

### P57 格式化输出
>JDK5.0后引入了printf方法 

可以使用多个参数：每一个以%字符开始的格式说明符都应用相应的参数替换。格式说明符尾部的转换符指被格式化的数值类型。
	
	System.out.printf("Hello,%s.Next year,you'll be %d",name,age);
	注：这里%s与后面的字符串可以不隔开；d代表十进制；
1. printf的转换符；
2. printf的标志；

		System.out.printf(%,.2f,10000.0/3.0);
		结果为：3，333.33
		保留2位小数并且用逗号分隔，逗号为printf的标志，f为printf的转换符。
格式化字符串的另一个方法：静态的`String.format`
	
		String message = String.format("Hello,%s.Next year,you'll be %d",name,age);
日期时间的转换符：两个字符格式以t开始

		System.out.printf("%tc",new Date());

格式说明符的语法：

