---
title: SpringMVC 基础
date: 2018-12-27 21:18:12
tags: [java]
category: java基础
---


# 基本例子

`web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>


    <!--前端控制器-->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

        <!--默认找  dispatcher-servlet.xml-->
        <!--<init-param>-->
            <!--<param-name>contextConfigLocation</param-name>-->
            <!--<param-value>/WEB-INF/applicationContext.xml</param-value>-->
        <!--</init-param>-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--
     / 拦截所有(除了jsp),建议
    -->
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

`dispatcher-servlet.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--扫描 @Controller, @Service-->
    <context:component-scan base-package="com.passon"/>

</beans>
```

```java
@Controller
public class ItemsController {

    @RequestMapping(value = "/item/items")
    public ModelAndView hello(){
        // 创建页面需要显示的商品数据
        List<Items> list = new ArrayList<Items>();
        list.add(new Items(1, "1华为 荣耀8", 2399f, new Date(System.currentTimeMillis()), "质量好！1"));

        ModelAndView mav = new ModelAndView();
        mav.setViewName("/jsp/itemList.jsp");
        mav.addObject("itemList", list);
        return mav;
    }
}
```

# SpringMVC 配置三大组件

1. HandlerMapping: 处理器映射器
2. ViewResolver: 视图解析器
3. HandlerAdapter: 处理适配器

## SpringMVC 其他设置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">


    <!--扫描 @Controller, @Service-->
    <context:component-scan base-package="com.passon"/>

    <!--处理映射器-->
    <!--<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>-->
    <!--处理适配器-->
    <!--<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>-->
    <!--注解驱动 代替上面两个-->
    <mvc:annotation-driven/>

    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 可以设置前缀 -->
        <property name="prefix" value="/WEB-INF/jsp"/>
        <!-- 设置后缀 -->
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

# 参数

```java
@RequestMapping(value = "/item/itemlist2.action")
public ModelAndView itemList2(HttpServletRequest request, HttpServletResponse response, HttpSession session, Model model) {
    Integer id = request.getParameter("id");
}
```

参数绑定：

```java
@RequestMapping(value = "/item/itemlist2.action")
public ModelAndView itemList2(Integer id, HttpServletRequest request, HttpServletResponse response, HttpSession session, Model model) {
    // id 直接使用
}
```

参数绑定，使用别名：(不推荐)

```java
@RequestMapping(value = "/item/itemlist2.action")
public ModelAndView itemList2(@RequestParam(value= "id", required = false, defaultValue = "1") Integer idaq) {
    // id 直接使用

}
```

参数自动封装：

```java
/**

name="username"
name="age"

*/
@RequestMapping(value = "/item/itemlist2.action")
public ModelAndView itemList2(User user) {
    

}
```

## 高级参数绑定

数组绑定:

```java
/**
<input type="checkbox" name="ids" value="">
*/
@RequestMapping(value = "/item/itemlist2.action")
public ModelAndView itemList2(Integer[] ids) {
    
}
```

```java
/**
<input type="checkbox" name="ids" value="">
*/
@RequestMapping(value = "/item/itemlist2.action")
public ModelAndView itemList2(QueryVo qv) {
    
}
```

集合绑定:

```java
/**
${itemList}

itemList[0]

<c:forEach ..../>
*/
@RequestMapping(value = "/item/itemlist2.action")
public ModelAndView itemList2(List<Item> itemList) {
    
}
```

# @RequestMapping 使用

## RequestMethod

```java
public enum RequestMethod {
    GET,
    HEAD,
    POST,
    PUT,
    PATCH,
    DELETE,
    OPTIONS,
    TRACE;

    private RequestMethod() {
    }
}
```

```java
@RequestMapping(value = "/hello", method = RequestMethod.POST)

@RequestMapping(value = "/hello", method = {RequestMethod.POST, RequestMethod.GET})
```

路径转化拼接：

```java
@Controller
@RequestMapping(value = "/item")
public class ItemController {

    // /item/hello
    @RequestMapping(value = "/hello", method = {RequestMethod.POST, RequestMethod.GET})
    public ModelAndView itemList2(HttpServletRequest request, HttpServletResponse response, HttpSession session, Model model) {
    }

}
```

设置多个 url 路径：

```java
@RequestMapping(value = {"/hello", "/hello2"}, method = RequestMethod.POST)
```

# Controller 方法返回值

1. ModelAndView: 可以设置数据，返回视图路径
2. String: 只能返回视图路径, 配合参数中的 Model 设置数据

```
// 重定向
return "redirect:/item/itemlist.action"
// 转发
return "forward:/item/itemlist.action"
```

3. void: 无任何功能 ajax 请求的时候使用, Model 设置数据，request 实现转发和重定向

# SpringMVC 异常处理

```java
public class CustomExceptionResolver implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        // obj: String 包名+类名+方法名
        // 日志
        ModelAndView mav = new ModelAndView();
        mav.addObject("error", "位置异常");
        mav.setViewName("error");

        return mav;
    }

}
```

注册：

```xml
<!-- 全局异常处理器 -->
<bean class="com.passon.ssm.exception.CustomExceptionResolver"/>
```

# 图片上传处理

配置：

```xml
    <!-- 文件上传 -->
    <bean id="multipartResolver"
          class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 默认编码 -->
        <property name="defaultEncoding" value="utf-8"/>
        <!-- 文件大小最大值 -->
        <property name="maxUploadSize" value="10485760000"/>
        <!-- 内存中的最大值 -->
        <property name="maxInMemorySize" value="40960"/>
    </bean>
```

接收：

```java
    @RequestMapping(value = "/upload")
    public ModelAndView uploadFile(MultipartFile postName) throws IOException {
        String name = UUID.randomUUID().toString().replaceAll("-", "");
        postName.transferTo(new File("//Users/double/uploadFile/" + name));
    }
```

# Json 数据交互

```java

     * json 接收
     * 
     * 返回 json
     */
    @RequestMapping(value = "/json.action")
    public @ResponseBody  Items json(@RequestBody Items items){
        
    }
```

# SpringMVC 实现 RESTFul

# 拦截器

```xml
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <mvc:mapping path="/user/**"/>
            <bean class="com.zkh.interceptor.LoginInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

拦截器执行顺序：

1. preHandle 按拦截器定义顺序调用
2. postHandle 按拦截器定义逆序调用
3. afterCompletion 按拦截器定义逆序调用


postHandle 在拦截器链内所有拦截器返回成功调用。
afterCompletion 只有 preHandle 返回 true 才调用。


# Struts2 与 SpringMVC 区别

1. 入口不一样， Filter Servlet
2. Action, Controller, 一个是多例，一个是单例
3. Struts2 中有值栈，SpringMVC 值放在 Request 域中

# SSM 框架整合







