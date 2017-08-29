---
title: 设计模式之蝉 - 观察者模式 vs 责任链模式
date: 2017-08-15 21:38:14
tags: 设计模式
categories: 读书笔记

---

DNS 服务器解析，区域 DNS 服务器保存自己区域的域名解析数据，如果解析不了就会提交上级域名解析，最终由美国洛杉矶的顶级域名服务器进行解析，返回结果。


# 责任链模式实现 DNS 解析过程



解析记录：

```java
public class Recorder{
    private String domain;
    private String ip;
    private String owner;
    //getter/setter/toString
}
```

抽象域名服务器；

```java
public abstract class DnsServer{
    private DnsServer upperServer;

    public final Recorder resolve(String domain){
        Recorder recorder = null;
        if(isLocal(domain)){
            recorder = echo(domain);
        }else{
            recorder = upperServer.resolve(domain);
        }
        return recorder;
    }

    public void setUpperServer(DnsServer _upperServer){
        this.upperServer = _upperServer;
    }

    // 判断 域名 是否是属于这个区域的
    protected abstract boolean isLocal(String domain);
    // 解析 域名
    protected Recorder echo(String domain){
        Recorder recorder = new Recorder();
        recorder.setIp(genIpAddress());
        recorder.setDomain(domain);
        return recorder;
    }
    private String genIpAddress(){
        Random rend = new Random();
        String address = rand.nextInt(255) + "." + rand.nextInt(255) + "." +rand.nextInt(255);
        return address;
    }
}
```

<!--more-->

上海 DNS 服务器：

```java
public class SHDnsServer extends DnsServer{
    @Override
    protected Recorder echo(String domain){
        Recorder recorder = super.echo(domain);
        recorder.setOwner("上海 DNS 服务器");
        return recorder;
    }

    @Override
    protected boolean isLocal(String domain){
        return domain.endsWith(".sh.cn");
    }
}
```


这里重写了 echo 方法，用于设置 Recorder 的服务器级别

中国顶级 DNS 服务器，全球顶级 DNS 服务器：ChinaTopDnsServer, TopDnsServer

使用：

```java
public class Client{
    public static void main(Stringp[] args) throws Exception{
        DnsServer sh = new SHDnsServer();
        DnsServer china = new ChinaTopDnsServer();
        DnsServer top = new TopDnsServer();

        china.setUpperServer(top);
        sh.setUpperServer(china);

        String domain = "www.xxx.sh.cn";
        //start
        sh.resolve(domain);
        //end

    }
}
```

# 触发链模式实现 DNS 解析过程



抽象 DNS 服务器：即使观察者也是被观察者

```java
public abstract class DnsServer extends Observable implements Observer{

    // 接收到事件后处理
    public void update(Observable arg0, Object arg1){
        Recorder recorder = (Recorder)arg1;
        if(isLocal(recorder)){
            recorder.setIp(genIpAddress());
        }else{
            responsesFromUpperServer(recorder);
        }
        sign(recorder);
    }

    public void setUpperServer(DnsServer dnsServer){
        super.deleteObservers();
        super.addObserver(dnsServer);
    }

    //向父 dns 请求解析，也就是通知观察者
    private void responsFromUpperServer(Recorder recorder){
        super.setChanged();
        super.notifyObservers(recorder);
    }

    // 每个 dns 服务器签上自己的名字
    protected abstract void sign(Recorder recorder);
    protected abstract boolean isLocal(Recorder recorder);

    // 生成一个 ip
    private String genIpAddress(){
        // create address
        return address;
    }
}
```


`responsFromUpperServer` 方法只能设置一个观察者，一般一个 DNS 服务器只有一个上级服务器。

`sing` 方法签名用于记录由谁解析出来的

上海 dns 服务器：

```java
public class SHDnsServer extends DnsServer{
    @Override
    protected void sign(Recorder recorder){
        recorder.setOwner("SH DNS")
    }
    @Override
    protected boolean isLocal(Recorder recorder){
        return recorder.getDomain().endsWith(".sh.cn");
    }
}
```

中国顶级DNS服务器，全球DNS服务器。


场景：

```java
public class Client{
    public static void main(String[] args) throws Exception{
        DnsServer sh = new SHDnsServer();
        DnsServer china = new ChinaTopDnsServer();
        DnsServer top = new TopDnsServer();

        china.setUpperServer(top);
        sh.setUpperServer(china);

        // domain 随机生成一个域名
        String domain = "......"
        Recorder recorder = new Recorder();
        recorder.setDomain(domain);
        sh.update(null, recorder);
        

    }
}
```

# 小结

触发链和责任链的区别

- 链中的消息对象不同：责任链中传递的消息对象的结构不能改变，触发链中可以改变
- 上下节点关系不同：责任链中上下节点没有关系，接收同样的对象。触发链上下级关系亲密，链中相邻的节点都是一个小团体
- 消息的分销渠道不同：责任链一个消息从链头到链尾移动。触发链消息的传递有很大的灵活性















