---
title: 设计模式之蝉 - 装饰模式 vs 适配器模式
date: 2017-08-11 21:38:14
tags: 设计模式
categories: 读书笔记

---


装饰模式和适配器模式在通用类图上没有太多相似点，差别比较大，但是功能上有相似的地方：都是包装作用，都是通过委托方式实现其功能。

装饰模式包装的事自己的兄弟类，适配器模式则是修饰非血缘关系类。

<!--more-->


# 用装饰模式描述丑小鸭

天鹅接口：

```java
public interface Swan{
    public void fly();
    public void cry();

    public void desAppearance();
}
```

丑小鸭类：

```java
public class UglyDucking implements Swan{
    public void cry(){
        //...

    }
    public void desAppaearance(){
        System.out.println("外形脏兮兮");
    }
    public void fly(){
        System.out.println("不能飞");
    }
}

```

抽象装饰类：

```java
public class Decratoe implements Swan{
    private Swan swan;

    public Decorator(Swan _swan){
        this.swan = _swan;
    }
    public void cry(){
        swan.cry();
    }
    public void desAppearance(){
        swan.desAppaerance();
    }
    public void fly(){
        swan.fly();
    }
}
```

外形变化装饰类：

```java
public class BeautifyAppearance extends Decorator{
    public BeautifyAppearance(Swan _swan){
        super(_swan);
    }

    @Override 
    public void desAppearance(){
        System.out.println("外表是纯白的，非常惹人爱");
    }
}
```

飞行装饰类：

```java
public class StrongBehavior extends Decorator{
    public StrongBehavior(Swan _swan){
        super(_swan);
    }
    public void fly(){
        System.out.println("会飞行了");
    }
}
```

装饰模式的装饰类实际是被装饰对象的子类，并且重写要被装饰的方法。

# 适配器模式实现丑小鸭


定义两个接口：鸭类接口和天鹅类接口

一个适配器：UglyDuckling


鸭类接口：

```java
public interface Duck{
    public void cry();
    public void desAppearance();
    public void desBehavior();
}
```

鸭子：

```java
public class Duckling implements Duck{
    public void cry(){
        System.out.println("叫声---")        
    }
    public void desAppearance(){
        System.out.println("外形 黄白")
    }
    public void desBehavior(){
        System.out.println("会游泳");
    }
}
```

白天鹅类：

```java
public class WhiteSwan implements Swan{
    public void cry(){
        System.out.println("..")
    }
    public void desAppaearance(){
        System.out.println("外形 纯白色，惹人爱");
    }
    public void fly(){
        System.out.println("飞行");
    }
}

```

把白天鹅当成小鸭子看待，通过适配器将白天鹅变成鸭子：

```java
public class UglyDucking extends WhiteSwan implements Duck{
    public void cry(){
        super.cry();
    }
    public void desAppearance(){
        super.desAppaearance();
    }
    public void desBehavior(){
        System.out.println("会游泳");
        super.fly();
    }
}
```



# 最佳实践

两个模式的不同点：

1. 意图不同：装饰模式为了加强对象的功能，适配器模式关注转化
2. 施与对象不同：装饰模式装饰对象必须是自己的同宗，适配器模式者必须是两个不同对象
3. 场景不同：装饰模式任何场景都能使用，适配器模式则是一个小补救模式
4. 扩展性不同：装饰模式容易扩展，不需要装饰了就可以去掉，而且装饰模式可以继续扩展；适配器模式只是两个不同对象之间沟通的桥梁。


















