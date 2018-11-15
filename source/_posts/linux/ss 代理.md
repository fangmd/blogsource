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
yum install python-setuptools && easy_install pip
pip install shadowsocks
```
sudo pip install shadowsocks

```
ssserver -p 8000 -k password -m rc4-md5 -d start
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


## 安装ipsec

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


参考：

- [http://wuchong.me/blog/2015/02/02/shadowsocks-install-and-optimize/](http://wuchong.me/blog/2015/02/02/shadowsocks-install-and-optimize/)