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

# 责任链模式实现 DNS 解析过程


















