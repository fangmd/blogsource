---
title: Listener&Filter
date: 2016-11-21 21:18:12
tags: [java]
category: java基础
---

# Listener

监听器

## Web 监听器

总共有 8 种，划分成三种类型

### 1. 监听三个作用域创建和销毁

request -- httpServletRequest
session -- httpSession
application -- ServletContext

ServletContextListener
    ServletContext 创建：启动 tomcat 服务器的时候
    ServletContext 销毁：tomcat 关闭的时候

```java
package com.passon.student_manager;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class MyServletContextListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("MyServletContextListener init");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("MyServletContextListener destory");
    }

}
```

注册监听：`WEB_INFO/web.xml`

```
         <listener>
            <listener-class>com.passon.student_manager.MyServletContextListener</listener-class>
         </listener>
```


ServletRequestListener
    request 创建：访问服务器上的任意资源
    request 销毁：服务器已经对请求作出响应

HttpSessionListener
    session 的创建：只要调用 getSession(html不会，jsp会，servlet会)
    session 的销毁：超时 30分钟，非正常关闭，正常关闭服务器(序列化)


ServletContextListener 作用：

1. 在初始化中，做一些代码初始化工作
2. 在初始化中，执行自定义的任务调度 (Timer)

HttpSessionListener 作用

1. 统计在线人数

### 2. 监听三个作用域属性状态变更

>监听在作用域中值的 添加，替换，移除

- ServletContextAttributeListener
- ServletRequestAttributeListener
- HttpSessionAttributeListener

### 3. 监听 httpSession 里面存值的状态变更

>这类监听器不用注册, 需要让 JavaBean 实现这个接口

1. HttpSessionBindingListener

监听对象与 sessoin 绑定状态

2. HttpSessionActivationListener

>JavaBean 需要实现序列化接口

用于监听 session 的值，是钝化(序列化)还是活化(反序列化)。

如何让 session 在一定时间内钝化?

配置:

1. tomcat 配置 conf/context.xml 里面配置；对服务器中所有项目都生效
2. 在 conf/Catalina/localhost/context.xml 对 localhost 域名的工程生效
3. web 工程中配置 META_INF/context.xml; 对当前工程生效

# Filter

>过滤器，对客户端发出的请求进行过滤。起到拦截作用

创建时间：服务器启动的时候
销毁时间：服务器关闭的时候

多个拦截器的先后顺序和注册顺序有关

请求的来回都会经过过滤器。

常用设置:

```
String[] urlPatterns() default {};
DispatcherType[] dispatcherTypes() default {DispatcherType.REQUEST};
```

## 实现一个过滤器

```java
@WebFilter(filterName = "FirstFilter", urlPatterns = "/firstFilter.jsp")
public class FirstFilter implements Filter {
    public void destroy() {
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        System.out.println("firstFilter....");

        // 放行
        chain.doFilter(req, resp);
    }

    public void init(FilterConfig config) throws ServletException {

    }
}
```

1. `init` 函数中可以获取 filter 的相关配置

## 自动登入功能


```java
/**
 * 自动登入过滤器
 */
@WebFilter(filterName = "FirstFilter", urlPatterns = "/index.jsp")
public class FirstFilter implements Filter {
    public void destroy() {
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {

        Object user = ((HttpServletRequest) req).getSession().getAttribute("user");
        if (user != null) {
            chain.doFilter(req, resp);
        }

        Cookie[] cookies = ((HttpServletRequest) req).getCookies();
        Cookie auto_login = CookieUtils.findCookie(cookies, "auto_login");
        if (auto_login == null) {
            chain.doFilter(req, resp);
        }

        String value = auto_login.getValue();
        String[] split = value.split("#");

        // 用户登入已经过时 session 中数据失效
        User o = new User();
        o.setUsername(split[0]);
        o.setPassword(split[1]);
        try {
            if (new UserDaoImpl().login(o)) {
                // login success
                ((HttpServletRequest) req).getSession().setAttribute("user", o);
            }
        } catch (SQLException e) {
            e.printStackTrace();
            chain.doFilter(req, resp);
        }

    }

    public void init(FilterConfig config) throws ServletException {

    }

}
```


### BeanUtils

`commons-beanutils-1.9.3.jar`

```
BeanUtils.populate(user, request.getParameterMap());
```

扩展支持类型：

```java
ConvertUtils.register(new Converter() {
    @Override
    public <T> T convert(Class<T> aClass, Object o) {
        return null;
    }
}, Date.class);
```




