---
title: 设计模式之蝉 - 策略模式 vs 桥梁模式
date: 2017-08-17 21:38:14
tags: 设计模式
categories: 读书笔记

---



# 策略模式实现邮件发送


定义一个邮件模版，有两个实现类：TextMail，HtmlMail 分别实现两种不同格式的邮件封装。

Mailserver 是一个环境角色，接收 MailTemplate 对象通过 sendMail 方法发送邮件。


抽象邮件：

```java
public abstract class MailTemplate{
    private String from;
    private String to;
    private String subject;
    private String context;

    //creater/getter/setter

}
```

这个抽象类中没有抽象方法，但是定义为抽象类为了保证这个类不能被实例化。


文本邮件：

```java
public class TextMail extends MailTemplate{

    // creater

    public String getContext(){
        // 文本类型设置邮件的格式为 text/plain

        context = context + "\n邮件格式为：文本格式"
        return context;
    }
}
```

<!--more-->

超文本格式邮件：HtmlMail

MailServer:

```java
public class MailServer{
    private MailTemplate m;
    public MailServer(MailTemplate _m){
        this.m = _m;
    }

    public void sendMail(){
        //.... m
    }
}
```


使用场景：

```java

public class Client{
    public static void maim(String[] args){
        MailTemplate m = new HtmlMail("a@a.com", "b@b.com", "content", "..")
        MailServer mail = new MailServer(m);
        mail.sendMail();
    }
}
```

策略模式完成的任务就是提供两种可以替换的算法。供上层模块调用。


# 桥梁模式实现邮件发送


桥梁模式关注的是抽象和实现的分离，它是结构型模式。

下面使用桥梁模式扩展邮件服务器，


邮件模版：

```java
public abstract class MailTemplate{
    //...

    // 相比较策略模式的 MailTemplate 增加下面的方法
    public void add(String sendInfo){
        context = sendInfo + context;
    }
}

```

文本邮件和超文本邮件类一样：TextMail,HtmlMail


邮件服务器：

```java
public abstract class MailServer{
    protected final MailTempalte m;
    public MailServer(MailTemplate _m){
        this.m = _m;
    }

    public void sendMail(){
        //... send mail
    }
}
```

环境变量角色：

```java
public class Postfix extends MailServer{
    pubic Postfix(MailTemplate _m){
        super(_m);
    }

    public void sendMail(){
        String context = "Received: from XXX"
        super.m.add(context);
        super.sendMail();
    }
}
```

每个邮件服务器在发送邮件的时候都留下自己的标记。

SendMail 邮件服务器：

```java
public class SendMail extends MailServer{
    pubic Postfix(MailTemplate _m){
        super(_m);
    }

    public void sendMail(){
        String context = "Received: from XXX"
        super.m.add(context);
        super.sendMail();
    }
}
```



















