---
title: Dagger2 官方教程学习记录
date: 2017-11-17 12:18:12
tags: Dagger
category: android

---



# Declaring Dependencies 申明依赖

`@Inject` 注解 添加到构造方法上： Dagger 应该使用的构造方法。

例子：

```java
class Thermosiphon implements Pump {
  private final Heater heater;

  @Inject
  Thermosiphon(Heater heater) {
    this.heater = heater;
  }

  ...
}
```



`@Inject` 注解 添加到成员变量上：表示这个对象创建的时候需要注入的对象

例子：

```java
class CoffeeMaker {
  @Inject Heater heater;
  @Inject Pump pump;

  //...
}
```

# Satisfying Dependencies 安全的依赖添加

`@Inject` 的局限性:

- 接口没有构造方法
- 第三方包中的类不能添加这个注解
- Configurable objects must be configured!

使用 `@Provides` 弥补这些缺点

例子：

```java
@Provides static Heater provideHeater() {
  return new ElectricHeater();
}
```

`@Provides` 注解必须在 `@Module` 中使用：

例子：

```java
@Module
class DripCoffeeModule {
  @Provides static Heater provideHeater() {
    return new ElectricHeater();
  }

  @Provides static Pump providePump(Thermosiphon pump) {
    return pump;
  }
}
```


# 一个完整的依赖注入例子


# Singletons and Scoped Bindings

```java
@Provides @Singleton static Heater provideHeater() {
  return new ElectricHeater();
}
```
















