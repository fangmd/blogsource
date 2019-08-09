---
title: SS 代理安装
date: 2016-01-26 11:38:14
tags: [SS]
categories: Linux

---

## install ss

Debian / Ubuntu:
```
apt-get install python-pip
pip install shadowsocks

// or install pip 

cd ~
wget -P Downloads/ https://svn.apache.org/repos/asf/oodt/tools/oodtsite.publisher/trunk/distribute_setup.py

sudo python Downloads/distribute_setup.py

sudo easy_install pip
```

CentOS:

```
sudo yum install epel-release
sudo yum install python-pip
pip --version

sudo pip install shadowsocks
```


```
ssserver -p 8000 -k password -m rc4-md5 -d start
./ssserver -p 9000 -k password -m rc4-md5 -d start

./ssserver -p 9001 -k password -m rc4-md5 -d start
./ssserver -p 9001 -k password -m aes-256-gcm -d start

```

or 使用配置文件进行配置，方法创建/etc/shadowsocks.json文件，填入如下内容：
```
{
    "server":"my_server_ip",
    "server_port":8000,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"rc4-md5"
}
```

```
ssserver -c /etc/shadowsocks.json -d start
```


## 安装ipsec(不配置)
>不确定和 bbr 是否可以共存，先使用 bbr 加速

```
wget https://raw.githubusercontent.com/quericy/one-key-ikev2-vpn/master/one-key-ikev2.sh
chmod +x one-key-ikev2.sh
bash one-key-ikev2.sh
```
（vps类型选2，其他不填，一路回车到底）


*如系统重启、重装、镜像或换机房，执行以下命令:

重新启动ipsec：
```
ipsec start
```

## 开启bbr加速

下载一键脚本

```
wget --no-check-certificate https://github.com/91yun/uml/raw/master/lkl/install.sh

```

赋予执行权限

```
chmod +x install.sh
```

安装bbr

```
./install.sh
```

判断bbr是否安装成功

```
ping 10.0.0.2
```

如果 10.0.0.2 能 ping 通说明成功， ping 不通说明失败

### 修改bbr转发端口

>shadowsocks 端口需要在 bbr 转发端口之内

1. 修改 /root/lkl/run.sh ，查找 9000-9999 ，改成你想要的端口段
2. 修改 /root/lkl/haproxy.cfg 查找 9000-9999 ，改成你想要的端口段
3. 重启 vps

## 设置开机自启动

参考：[https://blog.csdn.net/Aggressive_snail/article/details/50640187](https://blog.csdn.net/Aggressive_snail/article/details/50640187)

```
第一种方式：在/etc/rc.local文件中加入启动命令
这种方式适合linux用户手动配置自启动程序。
```




参考：

- [http://wuchong.me/blog/2015/02/02/shadowsocks-install-and-optimize/](http://wuchong.me/blog/2015/02/02/shadowsocks-install-and-optimize/)
- [https://iphp.pw/2018/01/31/openvz-ss-bbr/](https://iphp.pw/2018/01/31/openvz-ss-bbr/)