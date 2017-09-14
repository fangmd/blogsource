---
title: 设计模式之蝉 - 代理模式 vs 装饰模式
date: 2017-08-11 21:38:14
tags: 设计模式
categories: 读书笔记

---


结构类模式：适配器模式，桥梁模式，组合模式，装饰模式，门面模式，享元模式，代理模式。

它们都是通过组合类或对象产生更大结构以适应更高层次的逻辑需求

下面比较 代理模式和装饰模式

# 代理模式

通过代理欣赏表演


<!--more-->

IRunner:

```java
public interface IRunner{
    public void run();
}
```

Runner:

```java
public class Runner implements IRunner{
    public void run(){
        //...run
    }
}
```

RunnerAgent:

```java
public class RunnerAgent implements IRunner{
    private IRunner runnner;
    public RunnerAgent(IRunner _runner){
        this.runner = _runner;
    }

    public void run(){
        Random rand = new Random();
        if(rand.nextBoolean()){
            runner.run();
            }else{
                // do not run
            }
    }
}
```

上面的代理，没有明确代理的是哪个运动员，运动由调用者决定。

# 装饰模式


用装饰模式：实现给运动员增加装配的需求

+ 装饰类:

```java
public class RunnerWithJet implements IRunner{
    private IRunner runner;
    public RunnerWithJet(IRunner _runner){
        this.runner = _runner;
    }
    public void run(){
        System.out.println("加快运动员速度：为运动员增加喷气装置")
        runner.run();
    }
}
```

client：

```java
public class Client{
    public static void main(String[] args){
        IRunner liu = new Runner();
        liu = new RunnerWithJet(liu);

        liu.run();
    }
}
```


# 最佳实践

通过上面的代码可以看出，代理模式和装饰模式十分相似，代码实现也相似。

代理模式：把当前的行为或功能委托给其他对象执行，代理类负责接口限定：是否可以调用真实对象的方法。

装饰模式：保证接口不变的情况下加强类的功能，保证的是被修饰的对象功能比原来的对象丰富（也可以减弱），比如 DataOutputStream 封装了 FileOutputStream














