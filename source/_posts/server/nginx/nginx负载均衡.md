---
title: nginx 使用记录
date: 2019-09-29 13:18:12
tags: [nginx]
category: Server

---

nginx 实现负载均衡有5种方式:

1. 轮询（默认） 

不作任何其他配置时默认模式，平均分配请求，遇到 down 服务器自动跳过。

2. weight 设置权重

权重越高访问概率越大

```
upstream bakend {  
  server 192.168.0.14 weight=1;  // 1/3
  server 192.168.0.15 weight=2; // 2/3  
}  
```

3. ip_hash 

ip_hash 算法，根据 ip 算出一个 hash 值，将请求发送到数值对应的后端，同一个 ip 地址只会访问一个服务器。(可以解决session问题)


```
upstream bakend {  
  ip_hash;  
  server 192.168.0.14:88;  
  server 192.168.0.15:80;  
}
```

4. fair (第三方)

按照后端服务器的响应时间来分配，响应时间短的优先分配。

5. url_hash (第三方)

按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。

```
例：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法  

upstream backend {  
  server squid1:3128;  
  server squid2:3128;  
  hash $request_uri;  
  hash_method crc32;  
}
```

down 设置:

```
server 127.0.0.1:9090 down;
```


> 多个负载均衡方式可以同时使用

参考:

[https://blog.csdn.net/woshihaiyong168/article/details/54890404](https://blog.csdn.net/woshihaiyong168/article/details/54890404)