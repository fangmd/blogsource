---
title: SSH 远程登入设置
date: 2016-01-27 11:38:14
tags: [Linux, SSH]
categories: Linux

---

<!--more-->

# 快速登入

## 生成 密匙

```
ssh-keygen -r rsa
```

生成的密匙在 `~/.ssh/` 下面


## 将公钥放在远程机器

把mac下刚生成的public_key "id_rsa.pub"文件拷贝一份到远端服务器即将需要登录用户家目录下的.ssh/目录下，并命名为authorized_keys.

## 修改ssh配置文件
没有就自己创建

`~/.ssh/config`:

```
Host test1
Hostname 20.2.32.3
Post 22
User root
IdentityFile ~/.ssh/id_rsa
```

## 登入
```
ssh test1
```

如果没有生成 ssh 钥匙通过输入密码登入即可


# ssh 传输文件

scp

```
scp cool_stuff.txt sanjeev@example.com:.

scp 2765930_www.dreamua.com.key root@39.98.39.42:.

scp 2765930_www.dreamua.com.pem root@39.98.39.42:.

```

参考：

- [https://www.zhihu.com/question/30640159](https://www.zhihu.com/question/30640159)