---
title: django 部署
date: 2016-10-05 13:18:12
tags: [django, nginx, uWSGI]
category: Python

---


# uwsgi 使用

## uWSGI配置文件

Django 中配置文件地址在工程中

```
[uwsgi]
socket = 127.0.0.1:9000
#http = 127.0.0.1:8078
chdir = /Users/double/uwsgi-tutorial/mysite
env = DJANGO_SETTINGS_MODULE=mysite.settings
module = mysite.wsgi:application
pidfile=/tmp/mysite.pid
processes = 4
threads = 2
master=True
socket=/Users/double/uwsgi-tutorial/mysite.sock
stats = 127.0.0.1:8079 # 统计端口，统计服务器设置使用，不能和上面的端口重复
buffer-size = 30000
vacuum=True
max-requests=5000
daemonize=/var/log/uwsgi/mysite.log
#chmod-socket=664
```

## 启动

使用配置文件启动：

```
sudo uwsgi --ini [filename.ini]
```

测试启动：

```
uwsgi --http :8001 --wsgi-file test.py

uwsgi --socket :8001 --wsgi-file test.py

uwsgi --http :8001 --module mysite.wsgi

uwsgi --socket :8001 --module mysite.wsgi
```


```
the web client <-> the web server <-> the socket <-> uWSGI <-> Python
```

## stop

pid 文件在 uwsgi 配置文件中设置

```
sudo uwsgi --stop /tmp/mysite.pid
```


## nginx 配置文件

mac 中配置文件地址：`/usr/local/etc/nginx/nginx.conf`

通过：添加下面的代码隔离配置

```
include /usr/local/etc/nginx/servers/*;
```

在 servers 文件夹下添加项目的配置：如下 `blog.conf`

    # the upstream component nginx needs to connect to
    upstream django {
        # server unix:/Users/double/PycharmProjects/first_django/mysite/mysite.sock; # for a file socket
         server 127.0.0.1:8001; # for a web port socket (we'll use this first)
    }

    # configuration of the server
    server {
        # the port your site will be served on
        listen      8000;
        # the domain name it will serve for
        server_name localhost; # substitute your machine's IP address or FQDN
        charset     utf-8;

        # max upload size
        client_max_body_size 75M;   # adjust to taste

        # Django media
        location /media  {
            alias /Users/double/PycharmProjects/first_django/mysite/media;  # your Django project's media files - amend as required
        }

        location /static {
            alias /Users/double/PycharmProjects/first_django/mysite/static; # your Django project's static files - amend as required
        }

        # Finally, send all non-media requests to the Django server.
        location / {
            uwsgi_pass  django;
            include     /Users/double/PycharmProjects/first_django/mysite/uwsgi_params; # the uwsgi_params file you installed
        }
    }

最终通过访问 8000 端口，访问服务器

## 使用自定义的uWSGI配置文件运行uWSGI

    uwsgi --ini mysite_uwsgi.ini # the --ini option is used to specify a file

# nginx 

## nginx log 地址

mac: `/usr/local/Cellar/nginx/1.10.2_1/logs/error.log`

## 关闭,重启 nginx

nginx pid file: `"/usr/local/var/run/nginx.pid"` 

```
sudo nginx -s quit
sudo nginx
sudo nginx -s reload
```

# 问题

## Mac 访问权限的问题

在部署的时候发现：如果把 django 工程放在 文档 目录下会有访问权限的问题，把 django 工程放在用户根目录下就好了 `/Users/[User Name]/djangoblog/`

## 如果出现端口占用的问题

方法一：确认端口，关闭占用端口的进程

```
lsof -i :[port]

sudo kill [pid number]
```

方法二：（如果上面的方法没发现占用端口的程序使用这个方法）
查看下面的程序是否在运行, 关闭进程

```
ps -ax | grep nginx

ps -ax | grep uwsgi

sudo kill -9 [pid number]
```

## socket 文件访问权限限制

把工程目录移动到根目录即可

```
/root/djangoblog/blog

to

/djangoblog/blog
```

## vps linux

### uwsgi

需要打开 virtualenv 环境：`/root/blogenv/`

pid: `/tmp/djangoblog.pid`

log: `/var/log/uwsgi/djangoblog.log`






参考：

- [官方文档](http://uwsgi-docs.readthedocs.io/en/latest/tutorials/Django_and_nginx.html)
