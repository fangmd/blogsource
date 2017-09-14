---
title: 设计模式之蝉 - 工厂方法模式 vs 建造者模式
date: 2017-08-08 21:38:14
tags: 设计模式
categories: 读书笔记

---


# 创建者模式

- 工厂方法模式
- 建造者模式
- 抽象工厂模式
- 单例模式
- 原型模式


# 工厂方法模式 vs 建造者模式

工厂方法模式：注重整体对象的创建方法

建造者模式：注重部件构件的过程，旨在通过一步一步地构造创建复杂对象

例子：如果要创建一个超人，使用工厂模式就是直接创建一个超人，但是使用建造者模式需要组装超人的手，头。。等部分然后得到一个超人。

<!--more-->


## 工厂方法建造超人

ISuperMan

```java
public interface ISuperMan{
    public void specialTalent();
}
```

AdultSuperMan

```java
public class AdultSuperMan implements ISuperMan{
    public void specialTalent(){
        System.out.println("力大无穷");
    }
}
```

ChildSuperMan

```java
public class ChildSuperMan implements ISuperMan{
    public void specialTalent(){
        System.out.println("小超人，...")
    }
}
```

以上是产品类，下面写个工厂类

SuperManFactory

```java
public class SuperManFactory{
    public static ISuperMan createSuperMan(String type){
        if(type.equalsIgnoreCase("adult")){
            return new AdultSuperMan();
        }else if(type.equalsIgnoreCase("child")){
            return new ChildSuperMan();
        }else{
            return null;
        }
    }
}
```

## 建造者创建超人

在传统的建造者模式上使用模版方法，每个建造者必须返回一个产品，但是产品是如何制造的由各个建造者自己负责。

产品类

```java
public class SuperMan{
    private String body;
    private String specialTalent;
    private String specialSymbol;

    //getter / setter
}
```

抽象建造者：

```java
public abstract class Builder{
    protected final SuperMan superMan = new SuperMan();

    public void setBody(String body){
        this.superMan.setBody(body);
    }
    public void setSpecialTalent(String st){
        this.superMan.setSpecialTalent(st);
    }
    public void SpecialSymbol(String ss){
        this.superMan.setSpecialSymbol(ss);
    }
    public abstract SuperMan getSuperMan();
}
```

上面代码属于模版方法模式

成年超人：

```java
public class AdultSuperManBuilder extends Builder{
    @Override
    public SuperMan getSuperMan(){
        super.setBody("强壮的身体");
        super.setSpecialTalent("会飞行");
        super.setSpeccialSymbol("胸前带s标记");
        return super.superMan;
    }
}
```

未成年超人:

```java
//...
```

导演类：

```java
public class Director{
    private static Builder adultBuilder = new AdultSuperManBuilder();
    private static Builder childBuilder = new ChildSuperManBuilder();

    public static SuperMan getAdultSuperMan(){
        return adultBuilder.getSuperMan();
    }
    public static SuperMan getChildSuperMan(){
        return childBuilder.getSuperMan();
    }
}

```


# 最佳实践

工厂方法模式和建造者模式区别

1. 意图不同
2. 产品复杂度不同：工厂方法模式创建的产品一般都是单一性质的产品，而建造者模式创建的是复合产品，由各个部件复合而成。


















