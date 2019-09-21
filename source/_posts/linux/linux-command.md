---
title: Linux Command
date: 2016-12-25 12:18:12
tags: [Linux, Command]
category: Linux

---

# 文件权限修改

```
chmod
```

# 文件归属修改

查看当前用户名:

```
whoami
``

```
chown 用户 目录或文件名
```

# 查看硬盘使用情况

```
df -h
```

# 查看内存使用情况

```
free -h
```

#  查看文件内容

```
# 查看小文件
cat [fiel]
# 查看大量内容
more [file]  
```


# 查看文件大小

```
ls -lh
```

# 内核

插卡内核
```
uname -a

#or 

uname -r
```

# kill process by pid file

```
kill $(cat ~/code/posbox/posbox.pid)
```

-9  杀死父进程

``` 
kill -9 $(cat ~/code/posbox/posbox.pid)
```

# ps 进程管理

Process Status

>ps 为我们提供了进程的一次性的查看，它所提供的查看结果并不动态连续的；如果想对进程时间监控，应该用 top 工具。

```
ps [参数]
```

命令参数：
查看所有进程信息：`ps -A`

`ps ax`:

- a: all
- x: x参数会显示没有控制终端的进程。



# tail 监听文件

常用于 log 的监听

tail[必要参数][选择参数][文件]   

-f ： 表示循环读取

常用例子： 
```
tail -f [文件]
```

# 杀进程 uwsgi

```
ps ax | grep uwsgi
```

kill:
```
udo pkill -f uwsgi -9
```

# fuser

```
fuser: [-cfu] file ...
    -c  file is treated as mount point
    -f  the report is only for the named files
    -u  print username of pid in parenthesis
```

例子：查看谁正在使用数据库,并关闭该进程

```
fuser db.sqlite3

# db.sqlite3: 35756 

kill 35756
```

# crontab

```
[minute] [hour] [day] [month] [week] [command]
```

例子：

```
* * * * * scrapy crawl douban
```

# which 

```
which python3
```

获取某个命令的位置