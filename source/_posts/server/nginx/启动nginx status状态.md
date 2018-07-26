---
title: 启动 nginx status 
date: 2017-01-01 13:18:12
tags: [Python, nginx]
category: Python

---

system version : macos 10.12.2

# 启用 nginx status 配置

## 添加配置

```json
server {
    listen  *:8181 default_server;
    server_name _;
    location /ngx_status 
    {
        stub_status on;
        access_log off;
        #allow 127.0.0.1;
        #deny all;
    }
}
```

## 重启 nginx

```
sudo nginx -s reload
```

or

```
sudo nginx -s quit

sudo nginx
```

查看 nginx 配置文件的地址和是否启动成功
```
sudo nginx -t
```

## 访问

[http://127.0.0.1:8181/ngx_status](http://127.0.0.1:8181/ngx_status) 

可以看到的内容：

```
Active connections: 6 
server accepts handled requests
 34 34 58 
Reading: 0 Writing: 1 Waiting: 5 
```

解释：

- active connections – 活跃的连接数量
- server accepts handled requests — 总共处理了11989个连接 , 成功创建11989次握手, 总共处理了11991个请求
- reading — 读取客户端的连接数.
- writing — 响应数据到客户端的数量
- waiting — 开启 keep-alive 的情况下,这个值等于 active – (reading+writing), 意思就是 Nginx 已经处理完正在等候下一次请求指令的驻留连接.

# 前提条件

## mac brew nginx 安装额外模块

查看默认安装的模块有哪些：
```
brew options nginx
```
输出的内容是默认会安装的模块

### 安装 nginx-full

```
brew tap homebrew/nginx
```

再查看一下可选参数：
```
brew options nginx-full
```

如果已经安装了 nginx 可能需要先取消 link:
```
brew unlink nginx
# brew link nginx-full 
```

安装 nginx-full 添加模块

```
brew install nginx-full --with-geoip
```

## 编译 nginx

```
./configure --with-http_stub_status_module --with-http_ssl_module --with-http_sub_module 
```

## 问题

`Undefined symbols for architecture x86_64:`

```
--with-cc-opt="-I/usr/local/include -I/usr/local/opt/openssl/include"
--with-ld-opt="-L/usr/local/lib -L/usr/local/opt/openssl/lib"
```

## openssl 安装方法

```
brew install openssl
```

```
ln -s /usr/local/Cellar/openssl/{version}/include/openssl /usr/local/include/openssl
```