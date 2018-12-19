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

>适合多个 Model 的时候使用

## 模型驱动 (最常用)

```html
姓名：<input type="text" name="username" />
```

```java
public class UserAction extends ActionSupport implements ModelDriven<User> {

    private User user = new User();

    @Override
    public User getModel() {
        return user;
    }

    @Override
    public String execute() throws Exception {
        return NONE;
    }
}
```

>适合一个 Model 

## INPUT 逻辑视图配置

对应拦截器:

```
params: 接收参数
conversionError： 类型转换
validation: 数据校验
workflow: 判断是否有错误，没有到 Action, 有跳转到 INPUT 逻辑
```

配置错误回调

```xml
<package name="hello" extends="struts-default" namespace="/">
    <global-results>
        <result name="input">/jsp/error.jsp</result>
    </global-results>
</package>
```

显示错误信息：

```
<%@ taglib uri="/struts-tags" prefix="s" %>

<s:fielderror />
```

# 复杂类型的数据封装

## 封装数据到 List

```java
public class UserAction extends ActionSupport{

    private List<User> users;

    public void setUsers(List<User> user) {
        this.users = user;
    }

    @Override
    public String execute() throws Exception {
        return NONE;
    }
}
```


```html
    用户名称：<input type="text" name="users[0].name"> <br/>
    用户名称2：<input type="text" name="users[1].name"> <br />
```

## 封装数据到 Map

```java
public class UserAction extends ActionSupport{

    private Map<String, User> map;

    public void setMap(Map<String, User> map) {
        this.map = map;
    }

    @Override
    public String execute() throws Exception {
        return NONE;
    }
}
```

```html
    用户名称：<input type="text" name="map['one'].name"> <br/>
    用户名称2：<input type="text" name="map['two'].name"> <br />
```

# OGNL

OGNL：对象图导航语言 Object-Graph Navigation Language

主要使用点：访问 OGNL 上下文 和 ActionContext.

使用要素：

1. 表达式
2. 根对象
3. Context 对象

## Java 中使用 OGNL

例子：

```java
    @Test
    public void demo01() throws OgnlException {
        // 获取 Context
        OgnlContext ognlContext = new OgnlContext();
        // 获取根对象
        Object root = ognlContext.getRoot();

        // 访问对象的方法
        Object value = Ognl.getValue("'helloWorld'.length()", ognlContext, root);
        // 访问对象的静态方法
        Object valueStatic = Ognl.getValue("@java.lang.Math@random()", ognlContext, root);

        // root 存值
        User user = new User("fangmingdong"); // user.name
        ognlContext.setRoot(user);
        root = ognlContext.getRoot(); // **需要重新获取 root
        // 获取 root 中的值
        Object rootValue = Ognl.getValue("name", ognlContext, root);

        // get value from context
        ognlContext.put("userid", "userid-asdasdas");
        Object contextValue = Ognl.getValue("#userid", ognlContext, root);

        System.out.println(value);
    }
```

## Struts2 中使用 OGNL

引入标签库：

```
<%@ taglib prefix="s" uri="/struts-tags" %>
```

开启静态方法访问：

```
<constant name="struts.ognl.allowStaticMethodAccess" value="true"/>
```

jsp 中使用：

```html
<h2>访问对象方法</h2>
<s:property value="'struts'.length()"/> <br/>

<h2>访问静态方法</h2>
<s:property value="@java.lang.Math@random()"/> <br/>
```

### ValueStack 值栈

Struts 将 XWrok 对 Ognl 的扩展这一套机制封装起来，这个对象叫 ValueStack。

ValueStack 是 Struts 的一个接口，OgnlValueStack 是 ValueStack 的一个实现

ValueStack 类似一个数据中转站,ValueStack 贯穿整个 Action 的生命周期.

#### ValueStack 结构解析

一次请求创建 Action 后，也会创建一个 ValueStack

ValueStack(OgnlValueStack) 关键成员变量：

- root (CompoundRoot, 是一个集合类型): 存放 Object
- context (OgnlContext, 实现了 Map 接口)：存放 Web 开发对象的引用
    + request
    + session
    + application
    + parameters
    + attr

>获取 root 中的数据不需要加 #
>获取 context 中的数据需要加 #

jsp 中查看 ValueStack 结构：

```
<s:debug/>
```

#### ValueStack 与 ActionContext 的关系

ValueStack 创建位置：

`PrepareOperations.class`

```java
    public ActionContext createActionContext(HttpServletRequest request, HttpServletResponse response) {
        Integer counter = 1;
        Integer oldCounter = (Integer)request.getAttribute("__cleanup_recursion_counter");
        if (oldCounter != null) {
            counter = oldCounter + 1;
        }

        ActionContext oldContext = ActionContext.getContext();
        ActionContext ctx;
        if (oldContext != null) {
            ctx = new ActionContext(new HashMap(oldContext.getContextMap()));
        } else {
            ValueStack stack = ((ValueStackFactory)this.dispatcher.getContainer().getInstance(ValueStackFactory.class)).createValueStack();
            stack.getContext().putAll(this.dispatcher.createContextMap(request, response, (ActionMapping)null));
            ctx = new ActionContext(stack.getContext());
        }

        request.setAttribute("__cleanup_recursion_counter", counter);
        ActionContext.setContext(ctx);
        return ctx;
    }
```

`StrutsPrepareAndExecuteFilter.class` -> `PrepareOperations.java`

```
ValueStack stack = dispatcher.getContainer(),getInstance(ValueStackFactory.class).createValueStack();
```

ActionContext 创建：

```
ctx = new ActionContext(stack.getContext());
```

ActionContext 内部有 值栈的引用(值栈中的 Map)

#### 获取 ValueStack

1. 通过 ActionContext 获取

```
ValueStack valueStack = ActionContext.getContext().getValueStack();
```

2. 通过 Request 获取

```
ValueStack stack = (ValueStack) ServletActionContext.getRequest().getAttribute(ServletActionContext.STRUTS_VALUESTACK_KEY);
```

>一个 Action 实例 对应一个 ValueStack 实例


#### 操作值栈 root 值

1. 在 Action 中提供属性的 get 方法的方式(Action 中的属性会压入到值栈)
2. 获取 ValueStack 操作值

```java
// 设置对象
User user = new User("name---");
valueStack.push(user);

// 设置值
// valueStack.set("key", "asdsa"); // 创建一个 HashMap，压入栈
```

```html
<!-- 获取对象中的值 -->
<s:property value="name"/>

<!-- 获取集合中的数据 -->
<s:property value="list[0].name"/>
<s:property value="list[1].name"/>
```

>如果有两个 User，取栈顶
>这里的存储数据都是向 ValueStack 中的 root 中存数据

页面中获取值栈中的对象的数据：直接访问对象的属性就可以了(需要 getter)
页面中获取集合数据：`<s:property value="list[1].name"/>`


#### 操作值栈 context 值

存值：

```java
ServletActionContext.getRequest().setAttribute("name", "lisi");
ServletActionContext.getRequest().getSession().setAttribute("name", "slisi");
ServletActionContext.getServletContext().setAttribute("name", "clisi");
```

取值：

```html
<s:property value="#request.name"/>
<s:property value="#session.name"/>
<s:property value="#application.name"/>
<s:property value="#attr.name"/>
<s:property value="#parameters.name"/>
```

#### EL 操作 ValueStack 中的值

```html
${name}
```

原因：struts2 的框架底层对 `request.getAttribute(String name)` 进行了增强

### % 用法

强制使用 ognl 解析

```html
<input type="text" name="name" value="%{#request.name})">
```

### $

在配置文件中使用 OGNL

# Struts 拦截器

Intercepter 拦截器，拦截 Action, 可以拦截 Action 中具体的方法

Filter: 过滤器，过滤请求

## Struts 执行流程

## 自定义拦截器

实现 interceptor 接口，或者继承 AbstractInterceptor, MethodInterceptor

```java
public class IntercepterOne extends AbstractInterceptor {
```

配置 interceptor:

方式一在配置中注册：

```xml
<package>
    <interceptors>
        <interceptor name="one" class="com.passon.struts.intercepter.IntercepterOne"/>
        <interceptor name="two" class="com.passon.struts.intercepter.IntercepterTwo"/>
    </interceptors>

    <!-- 在 Action 中指定 Interceptor -->

        <action name="ognl" class="com.passon.struts.action.OgnlAction" method="ognlUI">
            <result name="success">/ognldemo.jsp</result>

            <!--引入自定义拦截器后，默认栈的拦截器就不执行了，需要手动引入-->
            <interceptor-ref name="defaultStack"/>
            <interceptor-ref name="one"/>
            <interceptor-ref name="two"/>

        </action>
</package>
```

方式二自定义拦截器栈：

```xml
<package>

        <interceptors>
            <interceptor name="one" class="com.passon.struts.intercepter.IntercepterOne"/>
            <interceptor name="two" class="com.passon.struts.intercepter.IntercepterTwo"/>
            
            <interceptor-stack name="myStack">
                <interceptor-ref name="defaultStack"/>
                <interceptor-ref name="one"/>
                <interceptor-ref name="two"/>
            </interceptor-stack>
        </interceptors>

</package>
```

# 通用标签库&UI标签库











