---
title: Linux learn
date: 2016-01-03 22:00:35
tags: Linux
categories: Linux

---

# Linux 系统目录结构

- `/bin`: Binary 缩写，存放经常使用的命令
- `/boot`: 存放启动 Linux 时使用的核心文件
- `/dev`： Device 缩写，存放 Linux 的外部设备文件	
- `/etc`：存放所有系统管理所需要的配置文件和子目录
- `/home`：用户主目录，在 Linux 中每个用户都有一个自己的目录，用户名作为目录名	
- `lib`：存放系统基本的动态链接共享库
- `lost+found`：系统非法关机的时候存放信息文件
- `/media`：系统自动识别的外部存储设备挂载地址
- `/mnt`：用于用户临时挂载别的文件系统
- `/opt`：主机额外安装软件所摆放的目录，比如安装 ORACLE 数据库可以放在这里
- `/proc`：
- `/root`：系统管理员
- `/sbin`：Super User 存放系统管理使用的系统管理程序
- `selinux`：Redhat/CentOS 特有的目录，是一个安全机制
- `srv`：存放服务启动之后需要提取的数据
- `/sys`
- `/tmp`：存放临时文件
- `/usr`：*重要目录*用户的很多应用程序和文件存放在这里
- `/usr/bin`：系统用户使用的应用程序
- `/usr/sbin`：超级用户使用的比较高级的管理程序和系统守护程序
- `/usr/src`:内核源码默认存放位置
- `/var`：通常把经常被修改的文件放在这里，比如 各种日志文件


# Linux 文件权限问题

![Linux 文件权限](http://www.runoob.com/wp-content/uploads/2014/06/363003_1227493859FdXT.png)

chmod 更改文件属性

Linux 文件基本权限有 9 个： owner/group/others 三种身份，每个身份有3个权限：read/write/execute

- r:4
- w:2
- x:1

```
chmod [-R] xyz [文件或目录]
```

- xyz : 就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的相加。
- -R : 进行递归(recursive)的持续变更，亦即连同次目录下的所有文件都会变更




