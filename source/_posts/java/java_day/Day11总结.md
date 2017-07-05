---
title: java -- day11
date: 2015-12-28 21:18:12
tags: [java,day]
categories: java基础

---


# Day 11
## 基本数据类型的包装类	
>char -- Charactor 
>int -- integer
### 基本数据类型与其包装类	
### 数据的装箱和拆箱
在Jdk1.5以后java加入了自动装箱和拆箱功能；	

装箱:将基本数据类型封装为对应的包装类型对象的过程。

    语法：①若jdk的版本>= 1.5,会自动装箱
      包装类型 对象 = 基本数据类型的数据（变量或者是值）;  
    如：int age = 12;
    Integer ageObj = age;
     
     ②若jdk的版本<1.5,必须手动装箱
       包装类型 对象 = new  包装类型(基本数据类型（变量或者值）); 
    如：int age = 3;
    Integer ageObj = new Integer(age);


拆箱:将包装类型的对象转换为对应的基本数据类型的过程。

    语法：①若jdk的版本>= 1.5,会自动拆箱
      基本数据类型 变量 = 包装类型的对象;
    如：Integer ageObj = new Integer("88");
    int age = ageObj;
    
     ②若jdk的版本<1.5,必须手动装箱
      基本数据类型 变量 = 包装类型的对象.方法（实参）;
    如：Integer ageObj = new Integer("88");
    int age = ageObj.intValue();

基本数据类型：byte,short,int ,long ,char,float,double -->兼容关系

对应的包装类型：Byte，Integer -->不兼容，不存在继承关系，有一点点关系：都是Number的子类型。

**Arrays工具类**：负责对数组进行排序、查询、填充等等操作。（主要是用来操作一维数组）

常用方法：

1. static int binarySearch(int[] a, int key)  使用二分法从一个排好序的数组中查询指定的元素，若存在，返回索引值；否则，返回负数。
2. static int[] copyOf(int[] original, int newLength)：是创建一个当前数组的备份(全新的数组)，且指定了长度（从旧数组中复制元素的个数） ，若长度>旧数组的长度，不足的部分使用默认值填充。
3. static int[] copyOfRange(int[] original, int from, int to)  ：同copyOf（xxx）,指定了索引范围，但是拷贝后的数组中不包含结束索引对应的元素。
4. static boolean equals(int[] a, int[] a2) ：比较两个数组的元素值是否完全相同，与元素的位置有关系。
5. static void fill(int[] a, int val) ：使用指定值对数组中的每一个元素进行初始化（或者是覆盖）。
6 .static void sort(int[] a) ：对应数组中的元素进行升序排列。
7. static String toString(int[] a)：以指定的格式将数组遍历输出。
## 字符串	
### String类	
常用方法：

构造方法：

1. String(byte[] bytes)：将参数指定的字节数组转换为String 的对象。
2. String(byte[] bytes, Charset charset) 将指定了字符集的字节数组转换为String 的对象。

普通方法：

1. char charAt(int index) --》取出字符串中对应索引的字符 index从0开始
2. int compareTo(String anotherString)--》比较两个字符串的大小（根据字典顺序）
3. String concat(String str)-->拼接两个字符串为一个新的字符串，等价于连接符“+”
4. boolean contains(CharSequence s)--》判断字符串对象中是否包含参数所指定的字符串
5. boolean endsWith(String suffix) -->判断字符串是否以指定的后缀结束
6. boolean equals(Object anObject) --》比较两个字符串对象的内容是否完全相同
7. static String format(String format,Object... args) --》使用指定的格式格式变长参数值。
8. byte[] getBytes() -->将一个字符串转换为字节数组
9. byte[] getBytes(Charset charset) --》将一个使用参数指定的字符集编号码的字符串转换为字节数组。
10. indexOf(int ch)--》从当前字符串中查找参数指定的字符，找到一个与之匹配的返回该字符在字符串中对应的索引值
11.int indexOf(int ch, int fromIndex)  --》从当前字符串中指定索引处往后查找参数指定的字符，找到一个与之匹配的返回该字符在字符串中对应的索引值
12. int indexOf(String str) --》从当前字符串中查找参数指定的字符串，找到一个与之匹配的返回该字符串第一个字符在字符串中对应的索引值 
13. boolean isEmpty()：判断字符串是否为空，就是其中不包含元素 ，与null是不等价。null->一个对象在堆中尚未开辟存储空间
14. int lastIndexOf(int ch)： 从当前字符串中从后往前查找参数指定的字符，找到一个与之匹配的返回该字符在字符串中对应的索引值 （索引：永远是从前往后计数）
15. int length() ：返回字符串中所包含的字符个数（无论是半角还是全角都占用一个字符）
16. ***boolean matches(String regex) ：用户判定当前字符串对象其格式是否与参数所指定的正则表达式相吻合
17. String replace(char oldChar, char newChar) ：使用新的字符替换字符串中旧的字符
18. String replace(CharSequence target, CharSequence replacement) ：使用新的字符序列替换字符串中旧的字符序列
19. String[] split(String regex) ：使用参数指定的正则表达式将字符串分割为一个字符串数组。（分割后的数组中元素不包含分隔符）
20. boolean startsWith(String prefix)：判断字符串对象是否以参数指定的前缀开始
21. String substring(int beginIndex) ：返回一个从参数指定的索引开始一直到结束的子串
22. String substring(int beginIndex, int endIndex) ：返回一个从参数指定的索引开始，指定的索引结束的子串，子串中不包含结束索引对应的字符
23. char[] toCharArray()：将一个字符串转换为字符数组
24. String toLowerCase()  ：将大写字符串转换为小写。（针对半角的英文字符有效）
25. String toUpperCase() ：将小写字符串转换为大写。（针对半角的英文字符有效）
16. String trim() :去掉字符串前后的半角空格。
27. static String valueOf(int i) ：将参数指定的int型的数据封装成字符串对象。
### StringBuffer类	
常用方法：

构造方法：

1. StringBuffer() 
2. StringBuffer(String str)

普通方法：

1. StringBuffer append(int i) ：将参数指定的int型的数据追加到字符串构建器的末尾。
2. int capacity() ：返回字符串构建器的容量
3. StringBuffer delete(int start, int end)：从字符串构建器中删除指定区间的字符序列。结束索引对应的字符不删除。
4. StringBuffer deleteCharAt(int index) ：将指定索引处的字符从 字符串构建器中删除
5. StringBuffer insert(int offset, int i) ：将参数指定的数值i插入指定的索引offset处，之后的元素自动后移。
6. int length() ：字符串构建器中字符的个数
7. StringBuffer replace(int start, int end, String str) ：使用指定的字符串替换指定区间的字符串。
8. StringBuffer reverse() ：将字符串中的字符反转。
9. void setCharAt(int index, char ch) ：使用指定的字符替换相应索引处的字符。

**StringBuffer与String的异同点：**

同：都是用来操作字符串的。

异：

1. 从生成对象个数的角度考虑，在拼接多个字符串为一个新的字符串的时候，String每拼接一个字符串，会生成一个全新的String字符串对象（堆，字符串串池）；StringBuffer每次追加一个数据时，操作的是同一块堆内存空间，只会生成一个对象。
2. 从效率来看，在拼接多个字符串时，StringBuffer的速度远远高于String.
3. 从消耗内存的角度看，在拼接多个字符串时，StringBuffer占用的内存要远远小于String.
### StringBuilder类 与 StringBuffer基本一样
**StringBuffer与StringBuilder异同点说明：**

同：都是字符串构建器，都是用来操作字符串的。

异：StringBuffer 线程安全的。（说明：在多线程并发访问该类相应方法时，同一时刻，只有一个线程能访问，该线程执行完后，别的线程才有可能执行该方法。）StringBuilder 线程不安全的。（说明：在多线程并发访问该类相应方法时，同一时刻，多个线程都有机会执行该方法，会造成临界资源访问的冲突）
## Math类	
  max(x)

  min（）

  abs()

  sqrt()

  pow()
### 常用方法	
### 静态导入	
>静态导入:为了方便使用工具类中的工具方法，导入时，将该方法直接引入，类体中只需要书写方法名即可。
>
>语法：import 类的全限定名.工具方法名；
>
>使用：在类体中直接--> 方法名(实参)
## Random类	
>Random：随机数生成器类，其中的方法nextInt(1)与Math类中的 random()工具方法作用一样。

常用方法：

构造方法：

1. Random(long seed)  --》使用的不多，注意：使用同一个种子生成的Random类对象，生成的随机数也相同。
2. Random() 

普通方法：

1. int nextInt()  --》随机产生一个int取值范围之内的数
2. int nextInt(int n) --》等价于Math.random()*n,取值范围是[0,n)，不同之处在于：返回值类型不同，Math.random()返回double
## System类	
>封装了一些与当前os进行信息交互的工具方法。

常用的属性：

1. System.in -->InputStream,“标准”输入流。
2. System.out -->PrintStream, “标准”输出流。
3. System.out.println("xx");

常用的方法：

1. static long currentTimeMillis() --》以毫秒值返回的系统当前时间
2. static long nanoTime()  --》以纳秒值返回的系统当前时间
3. static void exit(int status)  --》无条件终止jvm的执行
4. static void gc() --》调用该方法，给垃圾回收器一个暗示，至于垃圾回收器是否会立即执行，完全取决于其底层的算法。
5. static String getenv(String name)  --》根据参数指定的环境变量名获得其对应的环境变量值。
## Date类	
>对时间的抽取和封装，类 Date 表示特定的瞬间，精确到毫秒。

常用方法：

构造方法：

1. Date() ：表示系统当前时间。

普通方法：

1. boolean after(Date when)  测试此日期是否在指定日期之后。 
2. boolean before(Date when) 测试此日期是否在指定日期之前。  
3. int compareTo(Date anotherDate) 比较两个日期的顺序。 
4. boolean equals(Object obj) 比较两个日期的相等性。
## Calendar类	
1. Calendar类：Calendar 类是一个抽象类，它为特定瞬间与一组诸如 YEAR、MONTH、DAY_OF_MONTH、HOUR 等 日历字段之间的转换提供了一些方法，并为操作日历字段（例如获得下星期的日期）提供了一些方法。
**Calendar类中封装了一些操作日历的方法。**

常用方法：

1. int get(int field)  --》根据参数指定的日历字段，获得值。
2. static Calendar getInstance() --》返回Calendar类对象。
3. void set(int field, int value)--》设置日历实例某个属性的值。
4. void set(int year, int month, int date)---》定制当前日历对象中封装的具体时间  
## SimpleDateFormat类
>使用来将一个Date型的对象格式化输出，或者是将一个日>期格式的字符串封装成一个Date型的实例。	

SimpleDateFormat类常用的方法：

1. Date parse(String source) 从给定字符串的开始解析文本，以生成一个日期。
2. String format(Date date) 将一个 Date 格式化为日期/时间字符串。  

**过时：**某个类中的方法或者是属性不推荐使用了，目前有更好的替代方案。
如何达到让方法过时的目的？
语法：在方法之前添加注解：`@Deprecated`
	
### 正则表达式
>是一个特殊的字符串，用于验证别的字符串格式的合法性。

分类：

1. 字符类：[abc] a、b 或 c（简单类） --》当前字符串中的内部是a是b或者是c，是就匹配；否则，不匹配。
2. 预定义字符类

    . -->任何字符任何字符

    \d 数字：[0-9] 

    \D 非数字： [^0-9] 

    \s 空白字符：[ \t\n\x0B\f\r] 

    \S 非空白字符：[^\s] 

    \w 单词字符：[a-zA-Z_0-9] 

    \W 非单词字符：[^\w] 

3. 边界匹配器 

    ^ 行的开头 
    
    $ 行的结尾 

4. Greedy 数量词
	
	+：一个或者多个
	
	?：一次或0次
	
	*：0次或多次
	
	{n}:出现的次数恰好是n次
	
	{n,}:出现的次数至少是n次
	
	{n,m}:出现的次数至少是n次,最多是m次


### 枚举的简单使用
枚举：也是引用类型，用法与类类似。有七种用法。

在实际项目中使用最多是：**枚举常量**。

语法：访问权限修饰符 enum 枚举名{ 枚举常量1,枚举常量2,枚举常量3,...,枚举常量n
      
访问方式：枚举名.枚举常量

需求：定义一个封装了各个季节的枚举类，再测试（某人根据不同的季节进行相应的活动）

使用枚举的本质原因：提高代码的可读性。

使用时注意：switch分支语句的case分支后，是枚举常量值，直接书写即可。
