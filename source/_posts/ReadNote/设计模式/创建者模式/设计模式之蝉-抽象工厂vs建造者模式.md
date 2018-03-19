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


# 按建造者模式生产车辆

实现宝马发动机，奔驰车轮


车辆描述类：

```java
public interface ICar{
    public String getWheel();
    public String getEngine();
}
```

具体车辆：

```java
public class Car implements ICar{
    private String engine;
    private String wheel;
    public Car(String _engine, String _wheel){
        this.engine = _engine;
        this.wheel = _wheel;
    }
    public String getEngine(){
        return engine;
    }
    public String getWheel(){
        return wheel;
    }
    public String toString(){
        return "Wheel:" + wheel + "engine:" +engine;
    }
}
```

抽象建造者：

```java
public abstract class CarBuilder{
    private ICar car;
    // 设计蓝图
    private Blueprint bp;

    public Car buildCar(){
        return new Car(buildEngine(), buildWheel());
    }

    public void receiveBluePrint(Blueprint _bp){
        this.bp = _bp;
    }
    protected Blueprint getBlueprint(){
        return bp;
    }

    protected abstract String buildWheel();
    protected abstract String buildEngine();
}
```

生产蓝图类：

```java
public class Blueprint{
    private String wheel;
    private String engine;

    public Strng getWheel(){
        return wheel;
    }
    public void setWheel(String wheel){
        this.wheel = wheel;
    }
    public String getEngine(){
        return engine;
    }
    public void setEngine(String engine){
        this.engine = engine;
    }
}
```


宝马车建造车间：

```java
public class BWMBuilder extends CarBuilder{
    public String buildEngine(){
        return super.getBlueprint().getEngine();
    }
    public String buildWheel(){
        return super.getBlueprint().getWheel();
    }
}
```

奔驰车建造车间：

```java
public class BenzBuilder extends CarBuilder{
    public String buildEngine(){
        return super.getBlueprint().getEngine();
    }
    public String buildWheel(){
        return super.getBlueprint().getWheel();
    }
}
```

导演类：编写蓝图，协调生产车间，对外提供最终产品

```java
public class Director{
    private CarBuilder benzBuilder = new BenzBuilder();
    private CarBuilder bmwBuilder = new BWMBuilder();

    public ICar createBenzSuv(){
        return createCar(benzBuilder, "benz的引擎", "benz的轮胎");
    }

    public ICar createBWMVan(){
        return createCar(benzBuilder, "BWM的引擎", "BWM的轮胎");
    }

    private ICar createCar(CarBuilder _carBuilder, String engine, String wheel){
        Blueprint bp = new Blueprint();
        bp.setEngine(engine);
        bp.setWheel(wheel);
        _carBuilder.receiveBlueprint(bp)
        return _carBuilder.buildCar();
    }
}
```



