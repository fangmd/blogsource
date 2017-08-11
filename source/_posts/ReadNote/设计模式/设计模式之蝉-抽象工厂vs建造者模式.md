---
title: 设计模式之蝉 - 抽象工厂模式vs建造者模式
date: 2017-08-10 21:38:14
tags: 设计模式
categories: 读书笔记

---


抽象工厂模式实现对产品家族的创建，使用抽象工厂模式则是不需要关心构建的过程，只关心什么产品由什么工厂生产即可。

建造者模式是按照指定的蓝图建造产品，主要目的是通过组装零配件而产生一个新产品

<!--more-->

# 按抽象工厂模式生产车辆

汽车接口：

```java
public interface ICar{
    public String getBand();
    public String getModel();
}
```

抽象宝马：

```java
public abstract class AbsBWM implements ICar{
    private final static String BWM_BAND = "宝马车";
    public String getBand(){
        return BMW_BAND;
    }
    public abstract String getModel();
}
```


宝马商务车：

```java
public class BWMVan extends AbsBWM{
    private final static String SEVENT_SEARIES = "7系列车型商务车";
    public String getModel(){
        return SEVENT_SEARIES;
    }
}
```

宝马SUV：
```java
public class BWMSUV extends AbsBWM{
    private final static String X_SEARIES = "X系列车型SUV";
    public String getModel(){
        return X_SEARIES;
    }
}
```

同样的抽象奔驰，奔驰商务，奔驰SUV

抽象工厂类：

```java
public interface CarFactory{
    public ICar createSUV();
    public ICAR createVan();
}
```

宝马车工厂：

```java
public class BWMFactory implements CarFactory{
    public ICar createSuv(){
        return new BWMSuc();
    }
    public ICar createVan(){
        return new BWMVan();
    }
}
```

奔驰车工厂：

```java

//...

```















