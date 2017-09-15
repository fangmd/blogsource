---
title: 设计模式之蝉 - 门面模式vs中介模式
date: 2017-08-19 21:38:14
tags: 设计模式
categories: 读书笔记

---




门面模式为复杂的子系统提供一个统一的访问界面，它定义的是一个高层的接口，该接口使子系统更容易使用，避免外部模块深入到子系统。


中介者模式使用一个中介对象来封装一系列同事对象的交互行为。

# 中介者模式实现工资计算


抽象同事类：

```java
public abstract class AbsColleague{
    protected AbsMediator mediator;
    public AbsColleague(AbsMediator _mediator){
        this.mediator = _mediator;
    }
}
```

职位接口：

```java
public interface IPosition{
    public void promote();
    public void demote();
}
```

<!--more-->

职位：

```java
public class Position extends AbsColleague implements IPosition{
    public Position(AbsMediator _mediator){
        super(_mediator);
    }
    public void domote(){
        super.mediator.down(this);
    }
    public void promote(){
        super.mediator.up(this);
    }
}
```

每个职位的升降都委托给中介者执行。


工资接口：

```java
public interface ISalray{
    public void increaseSalary();
    public void decreaseSalary();
}
```

工资：

```java
public class Salary extends AbsColleague implements ISalary{
    public Salary(AbsMediator _mediator){
        super.(_mediator);
    }
    public void decreaseSalary(){
        super.mediator.down(this);
    }
    public void increaseSalary(){
        super.mediator.up(this);
    }
}
```

抽象中介者：

```java
public abstract class AbsMediator{
    protected final ISalary salary;
    protected final IPosition position;

    public Absmediator(){
        salary = new Salary(this);
        position = new Position(this);        
    }

    public abstract void up(ISalary _salary);
    public abstract void up(IPosition _position);

    public abstract void down(ISalary _salary);
    public abstract void down(IPosition _position);
}
```


中介者：

```java
public class Mediator extends AbsMediator{
    public void up(ISalary _salary){
        upSalary();        
    }

    public void up(IPosition position){
        upPosition();
        upSalary();
    }

    private void upSalary(){
        System.out.println("工资翻倍");
    }
    private void upPosition(){
        System.out.println("职位升级")
    }
    //...

}
```

场景类：

```java
public class Client{
    public static void main(String[] args){
        Mediator mediator = new Mediaotr();
        IPosition position = new Position(mediator);
        ISalary salary = new Sahalry(mediator);
        ITax tax = new Tax(mediator);

        position.promote();
    }
}
```


职位和工资, 税收之间有紧密的联系，如果不采用中介者模式，则每个对象都要与其他两个对象进行通讯，这会增加系统的复杂性。




# 门面模式实现工资计算


考勤情况：

```java
public class Attendance{
    public int getWorkDays(){
        return (new Ranom()).nextInt(30);
    }
}
```

奖金计算：

```java
public class Bonus{
    private Attendance atte = new Attendance();

    public int getBonus(){
        int workDays = atte.getWorksDays();
        int bonus = workDays * 1800 / 30;
        return bonus;
    }
}
```

基本工资：

```java
public class BasicSalary{
    public int getBasicSalary(){
        return 2000;
    }
}
```


绩效：

```java
public class performance{
    private BasicSalary salary = new BasicSalary();

    public int getPerformanceValue(){
        int perf = (new Random()).nextInt(100);
        return salary.getBasicSalary() * perf / 100;
    }
}
```

税收：

```java
public class Tax{
    public int getTax(){
        return (new Random()).nextInt(300)
    }
}
```

上面是所有的计算薪酬的子元素。

总工资计算：

```java
public class SalaryProvider{
    private BasicSalary basicSalary = new BasicSalary();
    private Bonus bonus = new Bonus();
    private Performance perf = new Performance();
    private Tax tax = new Tax();

    public int totalSalary(){
        return basicSalary.getBasicSalary() + .......;
    }
}

```


HR 门面：

```java
public class HRFacade{
    private SalaryProvider salaryProvider = new SalaryProvider();
    private Attendance attendance = new Attendance();

    public querySalary(String name, Date date){
        return salaryProvider.totalSalary();
    }
    public int queryWorkDays(String name){
        return attendance.getWorkDays();
    }
}
```

门面只是提供一个统一访问的基础而已。不做任何判断，校验，异常处理。


场景类：

```java
public class Client{
    public static void main(String[] args){
        HRFacade facade = new HRFacade();

        int salary = facade.querySalary("张三", new Date(System.currentTimeMillis()));

        int workDays = facade.queryWorkDays("李斯");        
    }
}
```



# 最佳实践

门面模式以封装和隔离为主要任务，中介者模式则是以调合同事类之间的关系为主，因为要调和，所以具有部分的业务逻辑控制。


- 功能区别：门面模式只是增加一个门面，对于子系统没有增加任何功能，子系统脱离门面可以完全独立运行。中介者模式则增加了业务功能，同事类通常不能脱离中介者而独立存在。
- 知晓状态：门面模式中子系统不知道有门面存在，中介者中每个同事类都知道中介者的存在。
- 封装程度不同：门面模式是一种简单的封装，任何请求处理都委托给子系统，中介者模式需要又一个中心，由中心协调同事类完成。



















