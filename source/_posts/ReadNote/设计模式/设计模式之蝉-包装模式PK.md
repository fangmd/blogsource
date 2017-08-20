---
title: 设计模式之蝉 - 代理模式
date: 2017-08-19 21:38:14
tags: 设计模式
categories: 读书笔记

---

在很多设计模式中有些角色是不干活的，只是冲当黔首的作用。

这类包装模式类型为：wrapping pattern

包括：装饰模式，适配器模式，门面模式，代理模式，桥梁模式


# 代理模式


明星接口：

```java
public interface IStar{
    public void sign();
}
```

明星：

```java
public class Singer implements IStar{
    public void sign(){
        System.out.pirintln("Singer sign");
    }
}
```

经纪人：

```java
public class Agent implements IStar{
    private IStar star;
    public Agent(IStar _star){
        this.star = _star;
    }
    public void sign(){
        star.sign();
    }
}
```

经纪人把请求传递给明星，让他签名。


# 装饰模式

明星接口:

```java
public interface IStar{
    public void act();
}
```

假明星：

```java
public class FreakStar implements IStar{
    public void act(){
        System.out.println("演技差");
    }
}
```

抽象装饰类：

```java
public abstract class Decorator implements IStar{
    private IStar star;

    public Decorator(IStar _star){
        this.star = _star;
    }
    public void act(){
        this.star.act();
    }
}
```

装饰类的子类：HotAir, Deny:

```java
public class HotAir extends Decorator{
    public HotAir(IStar _star){
        super.(_star);
    }

    public void act(){
        System.out.println("演出前：-----修饰动作")
        super.act();
    }
}
```


# 适配器模式


明星找提升


明星接口：

```java
public interface IStar{
    public void act(String context);
}
```

电影明星：

```java
public class FilmStar implements IStar{
    public void act(String context){
        System.out.println("明星演戏： " + context);
    }
}
```


普通演员接口:

```java
public interface IActor{
    public void playact(String context);
}
```


普通演员：

```java
public class UnknownActor implements IActor{
    public void playact(String context){
        System.out.println("普通演员：" + context);
    }
}
```

替身演员：

```java
public class Standin implements IStar{
    private IActor actor;
    public Standin(IActor _actor){
        this.actor = _actor;
    }
    public void act(String context){
        actor.playact(context);
    }
}
```


使用适配器，让普通演员也具有了：`act` 方法，同时实现了 `IStar` 接口

使用场景：

```java
public class Client{
    public static void main(String[] args){
        IStar star = new FilmStar();
        star.act("前十分钟，明星本人演出");

        IActor actor = new UnknownActor();
        IStar standin = new Standin(actor);
        standin.act("中间五分钟，提升演出");

        star.act("最后十分钟，明星演出");
    }
}
```

# 桥梁模式


抽象活动：

```java
public abstract class AbsAction{
    public abstract void desc();
}
```

演电影 ActFilm , 唱歌 Sing：

```java
public class Sing extends AbsAction{
    public void desc(){
        System.out.println("唱歌");
    }
}
```


抽象明星：

```java
public abstract class AbsStar{
    protected final AbsAction action;
    public AbsStar(AbsAction _action){
        this.action = _action;
    }
    public void doJob(){
        action.desc();
    }
}
```


电影明星：

```java
public class FilmStar extends AbsStar{
    public FilmStar(){
        super(new ActFilm());
    }
    public FilmStar(AbsAction _action){
        super(_action);
    }

    public void doJob(){
        System.out.println("..")
        super.doJob();
    }
}
```


场景类：

```java
public class Client{
    public static void main(String[] args){
        AbsStar zhangSan = new FilmStar();
        AbsStar lisi = new Singer();
        zhangSan.doJob();
        lisi.doJob();
        lisi = new Singer(new ActFilm());
        lisi.doJob();
    }
}
```


# 最佳实践

5种包装模式的共特征：都是通过委托的方式对一个对象或一些列对象实施包装，有了包装，设计的系统才更加灵活，稳定，并且极具扩展性。


代理模式：主要用在不希望展示一个对象内部细节的场景中。

装饰模式：是一种特殊的代理模式，提倡在不改变接口的前提下增加对象的功能。

适配器模式：主要意图是接口转换

桥梁模式：在抽象层产生耦合，解决的事自行扩展的问题，可以使两个有耦合关系的对象互不印象地扩展


门面模式：粗粒度的封装，提供一个方便访问子系统的接口，不具有任何的业务逻辑，仅仅是一个访问复杂系统的快速通道。















