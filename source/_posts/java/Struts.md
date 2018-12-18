---
title: Struts 基础
date: 2018-12-13 21:18:12
tags: [java]
category: java基础
---

# 概述

基于 MVC 设计模式的 Web 层框架，类似 Servlet。

Struts2 以 WebWork 为核心，采用拦截起的机制来处理用户的请求。

常见 Web 层框架：

1. Struts2
2. Struts1
3. Webwork
4. SpringMVC

传统方式：一个请求对应一个 Servlet。

前端控制器模型：所有请求都发送到 前端控制器，前端控制器在分发请求到具体的请求处理位置。

前端控制器可以由过滤器实现。

Struts2 执行流程：请求 -> 核心过滤器 -> 执行一组拦截器 -> 从配置中查找对应的 Action -> XXAction

# 配置

## 配置文件加载顺序

配置初始化位置：过滤器的 `init` 方法中

```java
FilterHostConfig config = new FilterHostConfig(filterConfig);
init.initLogging(config);
dispatcher = init.initDispatcher(config);
```

Dispatcher.class:

```java
// default.properties org/apache/struts2/default
init_DefaultProperties(); 
// struts-default.xml, struts-plugin.xml, struts.xml
init_TraditionalXmlConfigurations();
// struts.properties
init_LegacyStrutsProperties();
// 配置提供类
init_CustomConfigurationProviders();
// web.xml 中过滤器初始化的参数
init_FilterInitParameters();
// Bean对象
init_AliasStandardObjects();
```

加载顺序：

1. default.properties
2. struts-default.xml
3. struts-plugin.xml
4. struts.xml
5. struts.properties
6. web.xml

>后配置的常量值会覆盖先配置的常量值


## 常见配置

### package

包，用于管理 action 配置。

属性：

1. `name`: 包名称，不能与其他包重复
2. `extends`: 继承包, 通常值 `struts-default` 
3. `namespace`: 命名空间，与 `<action>` 标签中的 `name` 属性共同组成访问路径。 可以不写，不写的时候优先级最低
4. `abstract`: `true` 表示允许被继承

### action 

配置 action 类

属性：

1. `name`: 与 `namespace` 共同决定访问路径
2. `class`: Action 类全路径
3. `method`: 默认是 `execute`, 指定执行的函数名
4. `converter`:  用于自定义类型转换器，通常不使用

### 常量配置

`default.properties`

```
struts.i18n.encoding=utf-8   // 所有 post， get 请求的中文乱码不用处理了
struts.action.extension=action,,  // 请求默认的扩展名（.action, 或者没有扩展名）
```

修改常量位置：

`struts.xml`: (通常在这里改)

```xml
<!--修改常量-->
<!--<constant name="struts.action.extension" value="action"/>-->
```

`struts.properties`:

```
struts.action.extension=xyz
```

`web.xml`:

```xml
    <!-- Filters -->
    <!-- START SNIPPET: filter -->
    <filter>
        <filter-name>action2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>

        <init-param>
            <param-name>strust.action.extension</param-name>
            <param-value>xyz</param-value>
        </init-param>
    </filter>
```

### 分模块配置

```
<include file="struts2.xml"/>
```

# Action 访问

## Action 写法

### Action 是 POJO 类

POJO: 简单的 Java 类。

Action 类：

```
public class ActionOne {

    public String execute() {
        System.out.println("ActionOne execute");
        return null;
    }
}
```

配置中注册 Action:

```
<!-- 配置 Action -->
<action name="one" class="com.passon.struts.actiondemo.ActionOne">
</action>
```

### Action 实现一个 Action 的接口

Action 接口：

```java
package com.opensymphony.xwork2;

public interface Action {
    String SUCCESS = "success"; // 成功
    String NONE = "none";   // 不跳转
    String ERROR = "error"; // 失败
    String INPUT = "input"; // 表单校验的时候出错
    String LOGIN = "login"; // 登入出错页面跳转

    String execute() throws Exception;
}
```

实现类：

```java
public class ActionTwo implements Action {

    @Override
    public String execute() {
        System.out.println("ActionOne execute");
        return null;
    }
}
```

在配置中注册。

```
<action name="two" class="com.passon.struts.actiondemo.ActionTwo">
</action>
```
### Action 继承 ActionSupport 类(推荐使用)

ActionSupport 提供了：数据校验，国际化等一系列操作的方法。

```java
public class ActionThree extends ActionSupport {

    @Override
    public String execute() {
        System.out.println("ActionThree execute");
        return null;
    }
}
```

注册：

```java
<action name="three" class="com.passon.struts.actiondemo.ActionThree">
</action>
```

## Action 访问

目的：让多个请求由一个 Action 处理。

### 通过 method 设置

`UserAction.java`

```java
package com.passon.struts.actiondemo2;

import com.opensymphony.xwork2.ActionSupport;

public class UserAction extends ActionSupport {

    public String find() {
        System.out.println("find");
        return NONE;
    }

    public String update() {
        System.out.println("find");
        return NONE;
    }

    public String delete() {
        System.out.println("find");
        return NONE;
    }

    public String insert() {
        System.out.println("find");
        return NONE;
    }

}
```

注册：

```xml
        <!-- 配置 Action -->
        <action name="userFind" class="com.passon.struts.actiondemo2.UserAction" method="find">
        </action>

        <action name="userDelete" class="com.passon.struts.actiondemo2.UserAction" method="delete">
        </action>

        <action name="userInsert" class="com.passon.struts.actiondemo2.UserAction" method="insert">
        </action>

        <action name="userUpdate" class="com.passon.struts.actiondemo2.UserAction" method="update">
        </action>
```

### 通过通配符进行配置(常用)

注册

```xml
<action name="user*" class="com.passon.struts.actiondemo2.UserAction" method="{1}">
</action>
```

更抽象写法：(不常用)

```xml
<action name="*_*" class="xxx.{1}" method="${2}"></action>
```

### 动态方法访问

配置：

```xml
<action name="userfind" class="com.passon.struts.actiondemo2.UserAction">
</action>
```

需要开启动态方法访问：

```xml
<constant name="struts.enable.DynamicMethodInvocation" value='true'/>
```

路径访问：

```
/user!find
```

# Struts2 访问 Servlet API

## 完全解耦方式

获取参数：只能操作域对象中的数据

```java
ActionContext context = ActionContext.getContext();
HttpParameters parameters = context.getParameters();
Map<String, Object> session = context.getSession();
Map<String, Object> application = context.getApplication();
```

## 使用 Servlet 的 API 的原生方式

直接获取 `HttpServletRequest`, `HttpServletResponse`, `ServletContext`

```
ServletActionContext.getRequest();
ServletActionContext.getResponse();
ServletActionContext.getContext();
```

## 接口注入的方式(少用)

实现 `ServletRequestAware` 接口获取 `HttpServletRequest` 对象

`ServletContextAware`

>Servlet 是单例的，Action 是多例的。 Action 线程安全

# struts 结果页面配置

## 全局结果页面

在包中配置一次。

```xml
<!-- 全局结果页面 -->
<global-results>
    <result name="success">/success.jsp</result>
</global-results>
```

## 局部结果页面

只能在当前的 action 中的配置有效。

```xml
<action name="" class="">
    <result name="success">/sub_success.jsp</result>
</action>
```

## result 标签配置

1. name: 逻辑名称
2. type: 页面跳转类型
    1. dispatcher: 默认值，请求转发(->jsp)
    2. redirect: 重定向(->jsp)
    3. chain: 转发(->Action)
    4. redirectAction: 重定向 (->Action)
    5. stream: 文件下载


# 数据的封装

## 属性驱动

使用 `set` 方法的方式, 自动映射

```java
public class UserAction extends ActionSupport {

    private String username;
    private String password;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String execute() throws Exception {
        System.out.println(username + password);
        return NONE;
    }
}
```

```html
姓名：<input type="text" name="username" />
```

### set 2 常用

```java
public class UserAction extends ActionSupport {

    private User user;

    public User getUser(){
        return user;
    }
    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public String execute() throws Exception {
        System.out.println(username + password);
        return NONE;
    }
}
```

```html
姓名：<input type="text" name="user.username" />
```

## 模型驱动




# 复杂类型的数据封装






