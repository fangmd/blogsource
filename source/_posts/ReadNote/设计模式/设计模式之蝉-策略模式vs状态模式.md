---
title: 设计模式之蝉 - 策略模式 vs 状态模式
date: 2017-08-14 21:38:14
tags: 设计模式
categories: 读书笔记

---


状态模式和策略模式非常像

都是使用 Context 类封装一个具体的行为，都提供一个封装的方法。

策略模式封装的是不同的算法，算法之间没有交互。

状态模式封装的是不同的状态，以达到状态切换行为随之发生改变的目的。


# 策略模式实现人生


抽象工作算法：

```java
public abstract class WorkAlgorithm{
    public abstract void work();
}
```

具体算法，孩童算法：

```java
public class ChildWork extends WorkAlgorithm{
    @Override
    public void work(){
        System.out.println("儿童的工作是玩耍")
    }
}
```

AdultWork,OldWork


环境角色：

```java
public class Context{
    private WorkAlgorithm workMethod;

    // getter/setter

    public void work(){
        workMethod.work();
    }
}
```

使用的时候 需要创建 WorkAlgorithm 具体的子类，传入 Context 使用

# 状态模式实现人生

抽象状态类：

```java
public abstract class HumanState{
    protected Human human;

    public void setHuman(Human _human){
        this.human = _human;
    }

    public abstract void work();
}
```


状态具体类，ChildState，AdultState，OldState

```java
public class ChildState extends HumanState{
    public void work(){
        System.out.println("child play")
        super.human.setState(Human.ADULT_STATE);
    }
}
```

每个状态执行完成后切换状态，这里状态变化是单向的

环境角色：

```java
public class Human{
    public static final HumanState CHILD_STATE = new ChildState();
    public static final HumanState ADULT_STATE = new AdultState();
    public static final HumanState OLD_STATE = new OldState();

    private HumanState state;

    public void setState(HumanState _state){
        this.state = _state;
        this.state.setHuman(this);
    }

    public void work(){
        this.state.work();
    }

}
```



# 小结

策略模式和状态模式不同点：

- 环境角色的职责不同：策略模式的环境角色只是一个委托作用，负责算法的替换。状态模式的环境角色具有登记状态变化的功能，与具体的状态类写作，共同完成状态切换行为
- 解决问题的重点不同：策略模式旨在解决内部算法如何改变的问题，保证算法自由切换。状态模式旨在解决内在状态改变引起的变化问题
- 解决问题的方法不同
- 应用场景不同
- 复杂度不同











