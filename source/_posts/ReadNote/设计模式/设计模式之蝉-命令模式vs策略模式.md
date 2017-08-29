---
title: 设计模式之蝉 - 命令模式 vs 策略模式
date: 2017-08-08 21:38:14
tags: 设计模式
categories: 读书笔记

---


行为类模式：责任链模式，命令模式，解释器模式，迭代器模式，中介者模式，备忘录模式，观察者模式，状态模式，策略模式，模版方法模式，访问者模式。



命令模式和策略模式的类图相识，只是命令模式多了一个接受者角色

策略模式的意图是封装算法，让算法独立；命令模式则是动作解耦，把动作的执行对象分位执行对象（接收者），执行行为（命令角色）


# 策略模式实现压缩算法

一个文件可以使用 zip 或者 gzip 压缩

抽象压缩算法：

```java
public interface Algorithm{
    public boolean compress(String source, String to);
    public boolean unCompress(String source, String to);
}
```

<!--more-->

zip 压缩算法：

```java
public class Zip implements Algorithm{
    //...
}
```

gzip 压缩算法：

```java
public class GZip implements Algorithm{
    //...
}
```

环境角色：

```java
public class Context{
    private Algorithm al;
    public Context(Algorithm _al){
        this.al = _al;
    }
    public boolean compress(String source, String to){
        return al.compress(source, to);
    }
    public boolean uncompress(String source, String to){
        return al.uncompress(source, to)
    }
}
```

使用的时候创建 Context 就能压缩文件了，需要传入需要的算法对象，在更改算法的时候也只需要修改 Context 创建时传入的算法就可以了。



# 命令模式实现压缩算法

命令模式主旨是封装命令，使请求者和实现者解耦。

抽象压缩命令：

```java
public abstract class AbstractCmd{
    protected IReceiver zip = new ZipReceiver();
    protected IReceiver gzip = new GzipReceiver();

    public abstract boolean execute(String source, String to);
}
```

抽象命令引用了两个接受者：zip，gzip 接收者。

zip 压缩命令：

```java
public class ZipCompressCmd extends AbstractCmd{
    public boolean execute(String source, String to){
        return super.zip.compress(source, to);
    }
}
```

zip 解压命令，gzip压缩命令，gzip解压命令

抽象接收者：

```java
public interface IReceiver{
    public boolean compress(String source, String to);
    public boolean uncompress(String source, String to);
}
```

zip 接受者，gzip结束者


调用者：

```java
public class Invoker{
    private AbstractCmd cmd;
    public Invoker(AbstractCmd _cmd){
        this.cmd = _cmd;
    }
    public boolean execute(String source, String to){
        return cmd.execute(source, to);
    }
}
```


# 小结

两个模式的异同点

- 使用场景不同：策略模式适用于算法要求变换的场景，命令模式适用于解耦两个有紧耦合关系的对象场合或者多命令多撤销的场景














