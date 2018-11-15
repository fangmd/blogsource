---
title: Cookie&Seeeion
date: 2016-03-25 21:18:12
tags: [java]
category: java基础
---


会话技术：用于区分客户端

一次会话：从打开游览器访问某个站点，到关闭这个游览器的整个过程。

# Cookie

数据存储在客户端，安全性不好，客户端可以清除 cookie.

Cookie 默认级别是会话级别的，游览器关闭后就消失了。

## Servlet 操作 Cookie

```
//获取 Cookie
Cookie[] cookies = req.getCookies();
for (Cookie cookie : cookies) {
    if (cookie.getName().equals("age")) {
        age = Integer.parseInt(cookie.getValue());
    }
}

// 添加 Cookie
Cookie cookie = new Cookie("age", age + "");
// 设置 Cookie 持久化时间
cookie.setMaxAge(int seconds);
resp.addCookie(cookie);

// 设置 Cookie 携带路径: 防止 cookie 泛滥
cookie.setPath(String path);

// 删除 cookie, 创建添加一个同名 Cookie 并且设置持久化时间为 0，cookie 的路径要一致;
```

>如果不设置携带路径，那么该 cookie 信息会在访问该 cookie 的 web 资源所在的路径douxiedai cookie 信息。  /demo/setCookie 下产生的 cookie 只在 /demo/.. 生效。

# Session

数据存储在服务器端，安全性好，增加服务器压力

基于 cookie 技术，需要使用 cookie 存储 JSESSIONID.

Session 生命周期：

```
创建：第一次执行 req.getSession() 的时候
销毁：服务器关闭；session 过期(默认30min, 可以在 web.xml 中配置)；手动销毁 session `session.invalidate();`
```

Session 默认作用范围是一次会话，持久化方式和 cookie 一样。

## Servlet 操作 Session

```
// 创建和获取 session, 如果没有 session 会自动创建.
HttpSession session = req.getSession();
String id = session.getId();

// 设置数据到 session
session.setAttribute("key", "value");

// 获取 session 中存储的数据
Object key = session.getAttribute("key");

// session 持久化，设置 JSESSIONID 持久化时间
Cookie cookie = new Cookie("JSESSIONID", id);
cookie.setPath("");
cookie.setMaxAge(60*10*10*24);
resp.addCookie(cookie);
```


