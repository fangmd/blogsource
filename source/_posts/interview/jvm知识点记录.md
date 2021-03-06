---
title: JVM
date: 2019-08-12 21:18:12
tags: [jvm]
category: java基础
---


# JVM 工作流程

![jvm工作流程](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/jvm-work-process.png)

1. Java 编译器，编译源代码(class), 生成字节码文件(.class)
2. JVM 类加载器加载 .class 生成 Class 类。

# 运行时数据区 Runtime Data Area

![jvm内存](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/jvm-runtime-data-area.png)

| 区域 | 说明                      
|----------|-----|
| 程序计数器 | 每条线程都需要有一个程序计数器，计数器记录的是正在执行的指令地址，如果正在执行的是Natvie 方法，这个计数器值为空（Undefined） |
| java虚拟机栈 | Java方法执行的内存模型，每个方法执行的时候，都会创建一个栈帧用于保存局部变量表，操作数栈，动态链接，方法出口信息等。一个方法调用的过程就是一个栈帧从VM栈入栈到出栈的过程 |
| 本地方法栈 | 与VM栈发挥的作用非常相似，VM栈执行Java方法（字节码）服务，Native方法栈执行的是Native方法服务。| 
| Java堆 | 此内存区域唯一的目的就是存放对象实例，几乎所有的对象都在这分配内存 |
| 方法区 | 方法区是各个内存所共享的内存空间，方法区中主要存放被JVM加载的类信息、常量、静态变量、即时编译后的代码等数据 | 

# 方法指令

# 类加载器

| 类加载器 | 说明                      
|----------|-----|
| BootstrapClassLoader | Bootstrap类加载器负责加载rt.jar中的JDK类文件，它是所有类加载器的父加载器。Bootstrap类加载器没有任何父类加载器，如果你调用String.class.getClassLoader()，会返回null，任何基于此的代码会抛出NUllPointerException异常。Bootstrap加载器被称为初始类加载器 |
| ExtClasssLoader | 而Extension将加载类的请求先委托给它的父加载器，也就是Bootstrap，如果没有成功加载的话，再从jre/lib/ext目录下或者java.ext.dirs系统属性定义的目录下加载类。Extension加载器由sun.misc.Launcher$ExtClassLoader实现 |
| AppClassLoader | 第三种默认的加载器就是System类加载器（又叫作Application类加载器）了。它负责从classpath环境变量中加载某些应用相关的类，classpath环境变量通常由-classpath或-cp命令行选项来定义，或者是JAR中的Manifest的classpath属性。Application类加载器是Extension类加载器的子加载器 |
&nbsp;
| 工作原理 | 说明                      
|----------|------|
| 委托机制 | 加载任务委托交给父类加载器，如果不行就向下传递委托任务，由其子类加载器加载，保证java核心库的安全性 |
| 可见性机制 | 子类加载器可以看到父类加载器加载的类，而反之则不行 |
| 单一性机制 | 父加载器加载过的类不能被子加载器加载第二次 |

# GC

## 对象存活判断

1. 引用计数法

每个对象有一个引用计数属性，新增一个引用时计数加1，引用释放时计数减1，计数为0时可以回收。

优缺点：此方法简单，无法解决对象相互循环引用的问题。 


2. 可达性分析

从GC Roots开始向下搜索，搜索所走过的路径称为引用链。当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的。不可达对象。

优缺点：效率低

GC Roots 包括：

- 虚拟机栈中引用的对象。
- 方法区中类静态属性实体引用的对象。
- 方法区中常量引用的对象。
- 本地方法栈中JNI引用的对象。

## 垃圾回收算法

- **标记 -清除算法**
  
“标记-清除”（Mark-Sweep）算法，如它的名字一样，算法分为“标记”和“清除”两个阶段：首先标记出所有需要回收的对象，在标记完成后统一回收掉所有被标记的对象。之所以说它是最基础的收集算法，是因为后续的收集算法都是基于这种思路并对其缺点进行改进而得到的。

它的主要缺点有两个：一个是效率问题，标记和清除过程的效率都不高；另外一个是空间问题，标记清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致，当程序在以后的运行过程中需要分配较大对象时无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。

- **复制算法**
  
“复制”（Copying）的收集算法，它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。

这样使得每次都是对其中的一块进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。只是这种算法的代价是将内存缩小为原来的一半，持续复制长生存期的对象则导致效率降低。

- **标记-压缩算法**
  
复制收集算法在对象存活率较高时就要执行较多的复制操作，效率将会变低。更关键的是，如果不想浪费50%的空间，就需要有额外的空间进行分配担保，以应对被使用的内存中所有对象都100%存活的极端情况，所以在老年代一般不能直接选用这种算法。

根据老年代的特点，有人提出了另外一种“标记-整理”（Mark-Compact）算法，标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存

- **分代收集算法**

GC分代的基本假设：绝大部分对象的生命周期都非常短暂，存活时间短。

“分代收集”（Generational Collection）算法，把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。而老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用“标记-清理”或“标记-整理”算法来进行回收。







