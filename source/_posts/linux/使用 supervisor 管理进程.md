---
title: 使用 supervisor 管理进程
date: 2017-01-05 11:38:14
tags: [Supervisor]
categories: Linux

---

Supervisor 222(http://supervisord.org) 是一个用 Python 写的进程管理工具，可以很方便的用来启动、重启、关闭进程（不仅仅是 Python 进程）。除了对单个进程的控制，还可以同时启动、关闭多个进程，比如很不幸的服务器出问题导致所有应用程序都被杀死，此时可以用 supervisor 同时启动所有应用程序而不是一个一个地敲命令启动。

# Install

```
sudo pip install supervisor
```

or 

```
brew install supervisor
```

<!--more-->

# Supervisord 配置

把配置分成两部分：

- supervisord（supervisor 是一个 C/S 模型的程序，这是 server 端，对应的有 client 端：supervisorctl）
- 应用程序（即我们要管理的程序）

使用 brew 安装 supervisor 后，其配置文件在 `/usr/local/etc/supervisord.ini`

配置如下（去除了部分注射的代码）

```ini
[unix_http_server]
file=/usr/local/var/run/supervisor.sock   ; (the path to the socket file)
;chmod=0700                 ; socket file mode (default 0700)
;chown=nobody:nogroup       ; socket file uid:gid owner
;username=user              ; (default is no username (open server))
;password=123               ; (default is no password (open server))

;[inet_http_server]         ; inet (TCP) server disabled by default
;port=127.0.0.1:9001        ; (ip_address:port specifier, *:port for all iface)
;username=user              ; (default is no username (open server))
;password=123               ; (default is no password (open server))

[supervisord]
logfile=/usr/local/var/log/supervisord.log ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB        ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10           ; (num of main logfile rotation backups;default 10)
loglevel=info                ; (log level;default info; others: debug,warn,trace)
pidfile=/usr/local/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
nodaemon=false               ; (start in foreground if true;default false)
minfds=1024                  ; (min. avail startup file descriptors;default 1024)
minprocs=200                 ; (min. avail process descriptors;default 200)

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///usr/local/var/run/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; should be same as http_username if set
;password=123                ; should be same as http_password if set
;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
;history_file=~/.sc_history  ; use readline history if available

[include]
files = /usr/local/etc/supervisor.d/*.ini
```

运行 supervisord

```
supervisord -c /usr/local/etc/supervisord.ini
```

# program 配置

添加我们要管理的进程的配置文件，把所有配置项都写到 supervisord.ini 文件中，可以通过 include 方式把不同的程序（组）写到不同的配置文件里。

```
[include]
files = /usr/local/etc/supervisor.d/*.ini
```

如果 `/usr/local/etc/` 目录中没有 `supervisor.d` 文件夹，需要自己创建.

这里以启动 django 程序为例，创建一个配置文件 `/usr/local/etc/supervisor.d/djangoblog.ini`:

```
[program:blog]
directory = /path/to/dajngoprojects/ ; 程序的启动目录
command = python3 manager.py run server 0.0.0.0:8080  ; 启动命令，可以看出与手动在命令行启动的命令是一样的
autostart = true     ; 在 supervisord 启动的时候也自动启动
startsecs = 5        ; 启动 5 秒后没有异常退出，就当作已经正常启动了
autorestart = true   ; 程序异常退出后自动重启
startretries = 3     ; 启动失败自动重试次数，默认是 3
user = leon          ; 用哪个用户启动
redirect_stderr = true  ; 把 stderr 重定向到 stdout，默认 false
stdout_logfile_maxbytes = 20MB  ; stdout 日志文件大小，默认 50MB
stdout_logfile_backups = 20     ; stdout 日志文件备份数
; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile = /data/logs/usercenter_stdout.log
```

一份配置文件至少需要一个 `[program:x]` 部分的配置，来告诉 supervisord 需要管理那个进程。`[program:x]` 语法中的 x 表示 program name，会在客户端（supervisorctl 或 web 界面）显示，在 supervisorctl 中通过这个值来对程序进行 start、restart、stop 等操作。

# 使用 supervisorctl

Supervisorctl 是 supervisord 的一个命令行客户端工具，启动时需要指定与 supervisord 使用同一份配置文件，否则与 supervisord 一样按照顺序查找配置文件。

```
supervisorctl -c /usr/local/etc/supervisord.ini
```

上面这个命令会进入 supervisorctl 的 shell 界面，然后可以执行不同的命令了：
```
> status    # 查看程序状态
> stop usercenter   # 关闭 usercenter 程序
> start usercenter  # 启动 usercenter 程序
> restart usercenter    # 重启 usercenter 程序
> reread    ＃ 读取有更新（增加）的配置文件，不会启动新添加的程序
> update    ＃ 重启配置文件修改过的程序
```

或者 直接在 bash 终端运行：
```
$ supervisorctl status
$ supervisorctl stop usercenter
$ supervisorctl start usercenter
$ supervisorctl restart usercenter
$ supervisorctl reread
$ supervisorctl update 
```

