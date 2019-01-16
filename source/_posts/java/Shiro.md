---
title: Shiro 使用
date: 2018-12-19 21:18:12
tags: [java]
category: java基础
---



# 介绍 

Apache Shiro is a powerful and easy to use Java security framework that offers developers an intuitive yet comprehensive solution to authentication, authorization, cryptography, and session management.


# 基本使用

在任意地方回去当前发起请求的用户:

```java
Subject currentUser = SecurityUtils.getSubject();
```

`Subject` 封装了，当前访问的对象。

通过 `Subject` 可以设置 `Session`:

```java
Session session = currentUser.getSession();
session.setAttribute( "someKey", "aValue" );
```

用户登入操作：

```java
if ( !currentUser.isAuthenticated() ) {
    //collect user principals and credentials in a gui specific manner
    //such as username/password html form, X509 certificate, OpenID, etc.
    //We'll use the username/password example here since it is the most common.
    //(do you know what movie this is from? ;)
    UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
    //this is all you have to do to support 'remember me' (no config - built in!):
    token.setRememberMe(true);
    currentUser.login(token);
}
```














