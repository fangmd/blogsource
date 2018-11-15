---
title: JSP/EL/JSTL
date: 2016-03-26 21:18:12
tags: [java]
category: java基础
---


# JSP

在 html 中嵌入 java 代码

## JSP 脚本和注释

```
<% java 代码 %>：代码翻译后在方法内部

<%=java 变量或表达式>：代码翻译成 out.write("");

<%! java代码 %> ： 代码翻译后在成员位置
```

## JSP 运行原理

jsp 文件会被编译成 servlet java 文件。

第一次访问->Hello.jsp->Hello_jsp.java-> Hello_jsp.class 编译运行

>Hello_jsp.servlet 在 tomcat wrok 文件夹下

## JSP 指令

page 指令

```
language: java, jsp 可以嵌入的语言
pageEncoding： UTF-8, 文件编码格式
contentType: text/html;charset=UTF-8; 翻译成 response.setContentType("text/html;charset=UTF-8")
import: 导包
errorPage: "/errorPage.jsp" 设置错误页面
isErrorPage: true, 当前页面属于错误页面，可以拿到错误信息，通常在 web.xml 中设置全局的错误页面
```

include:

```
<%@ include file="被包含的文件地址" % >
```

taglib:jsp 页面引入标签库

```
<%@ tablib url="http://..." prefix="c" %>
```

## JSP 九大隐式对象

```
out，JspWriter, 用于页面输出
request, HttpServletReuest, 得到用户请求信息
response, HttpServletResponse, 服务器向客户端返回的信息
session, HttpSession, 用来保存用户信息
pageContext, PageContext, JSP 的页面容器 上下文，可以像指定域中存数据
config, ServletConfig, 服务器配置，可以获取初始化参数
exception, Throwable, 页面错误信息
application, ServletContext, 所有用户的共享信息
```

out 知识点:

```
out.write("bb");
response.getWriter().write("ccc");

上面两种方式输出,cc 先出现，因为 out 输出的缓冲区和 response 的缓冲区不是同一个。tomcat 从 response 缓冲区拿数据，out 缓冲区的数据会在数据满(默认8kb)的时候写入到 response 缓冲区中
```

pageContext 知识点：

```
// pageContext 向其他域中存取数据
pageContext.setAttribute("name", "lisi", PageContext.REQUEST_SCOPE);
pageContext.setAttribute("name", "lisi", PageContext.SESSION_SCOPE);
pageContext.setAttribute("name", "lisi", PageContext.APPLICATION_SCOPE);

pageContext.getAttribute("name", PageContext.APPLICATION_SCOPE);

//由小到大葱搜索到的域中取出数据
pageContext.findAttribute("name");

// 可以获取所有隐式对象
pageContext.getOut();
pageContext.getResuest();
```

>四个域对象：page, request, session, application

## JSP 标签

三个动作标签

```
<jsp:forward page="">
<jsp:include page="">
<jsp:param name="" value="">
```

静态包含:

```
<%@ include file="" %>
```

动态包含：

```
<jsp:include page="">
```

## 显示列表数据的例子

ProductServlet: 获取数据列表 List<Product>

product_list.jsp: 显示数据列表

ProductServlet, 请求转发, 传递数据到 jsp

```
request.setAttribute("productList", productList);
request.getRequestDispatcher("/product_list.jsp").forward(request, response);
```

product_list.jsp

```
<%
    List<Product> list = request.getAttribue("productList")
    for(Product p: list){
        out.write("<div " + p.getName() + ">")
    }
%>
```

# EL

Express Language 表达式可以嵌入在 jsp 页面内部，减少 jsp 脚本的编写。

EL 从域中取出数据。EL 比导师替代 jsp 中 java 代码。

```
$()

<%
    User user = new User();
    pageContext.setAttribute("user", user);
%>
$(user.name) --> 从四个作用域中，由小到大获取值
$(sessionScope.user.name) --> 强制取值范围

$(pageContext.request.contextPath)
```

11 个隐式对象：

```
pageContext

pageScope
requestScope
sessionScope
applicationScope

header
headerValues

param
paramValues

cookie
initParam
```

EL 表达式

```
$(1+1)
$(empty user)
$(user==null?true?false)
```


# JSTL

需要导入包：`jstl.jar`，`standard.jar`

JSP Standard Tag library, JSP 标准标签库

1. 导包 `<%@ taglib uri="http://java.sun.com/jsp/jst/core" prefix="c" %>`
2. 引入标签库(1.1)
3. 常用标签

```
<c:set>
<c:if>
<c:forEach>
```


配合 EL 表达式使用：

```
<%
    request.setAttribute("count", 10);
%>

<c:if test="$(count == 10)">
    .....
</c:if>
```

forEach:

```
<c:forEach begin="0" end="5" var="i">
    $(i)<br/>
</c:forEach>

<c:forEach item="$(productList)" var="product"></c:forEach>

<c:forEach item="$(itemMap)" var="entrty">
    $(entrty.key) ,, $(entrty.value)
</c:forEach>
```

# JavaEE 开发模式发展历程

model1 模式:

```
技术组成：jsp + JavaBean
```

model2 模式

```
技术组成：jsp + servlet + JavaBean
优点：开发中使用了各个技术擅长的方面
    servlet: 擅长处理 java 业务代码
    jsp: 擅长页面的实现
```

MVC，即 model2 模式（开发模式）

```
M: Model -- 模型 JavaBean
V: View -- jsp
C: Controller -- Servlet
```

## JavaEE 三层架构

Web 层：与客户端交互，servlet, javaBean, jsp，structs2, springMVC

Service 层：负责业务, 业务代码

dao 层：与数据库交互,操作数据库的代码, hibernate, mybatis




