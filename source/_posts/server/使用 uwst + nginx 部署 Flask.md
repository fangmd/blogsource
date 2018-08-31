---
title: 使用 uwst + nginx 部署 Flask
date: 2018-07-27 13:18:12
tags: [Python]
category: Server

---

>基于使用 PyCharm 创建的 Flask 工程


# 0 使用 PyCharm 创建一个 Flask 工程

跟目录文件是 `app.py`

# 1 启动 uwsgi

>所有命令行在 PyCharm 中的 terminal 中执行，PyCharm 中的 terminal 已经内置了 Pyhon 的开发环境

测试 uwsgi 是否正常：

```
uwsgi --socket 0.0.0.0:5000 --protocol=http -w app:app
```

打开网页 `http://localhost:5000` 看到 `Hello World` 表示流程通过

创建 uwsgi 配置文件，这样就不用每次启动都打命令行了：

`uwsgi_config.ini`

```
[uwsgi]
module = app:app
master = true
processes = 3

chdir = /Users/double/PycharmProjects/FlaskUwsgiNginxDemo

socket = /Users/double/PycharmProjects/FlaskUwsgiNginxDemo/sock/uwsgi.sock
http = 0.0.0.0:5000

logto = /Users/double/PycharmProjects/FlaskUwsgiNginxDemo/log/uwsgi.log
chmod-socket = 660
vacuum = true

stats = 127.0.0.1:9999 # 统计端口，统计服务器设置使用，不能和上面的端口重复
```

>注意需要创建配置文件使用到的文件夹和文件，除了 uwsgi.sock，
>这里设置了两种输出方式：http, sock，后面可以把 http 输出删除

使用, 启动 uwsgi：

```
uwsgi --ini uwsgi_config.ini
```

访问：`http://127.0.0.1:5000/` 显示网页

到此：Flask -> uwsgi 通过，下一步 uwsgi -> nginx

# 2 配置 nginx

mac nginx 配置文件位置： `/usr/local/etc/nginx/nginx.conf`

通常在 `/usr/local/etc/nginx/` 文件夹下创建 `servers` 文件夹用户存放所有服务器配置文件。

创建文件 `/usr/local/etc/nginx/servers/FlaskUwsgiNginxDemo.conf`

连接 sock:(没有调通)

```
server {
    listen 80;
    server_name 0.0.0.0;

    location / {
        include uwsgi_params;
        uwsgi_pass unix:/Users/double/PycharmProjects/FlaskUwsgiNginxDemo/sock/uwsgi.sock;
    }
}
```

连接 uwsgi http 输出：

```
server{  
    listen 80;
    server_name 0.0.0.0; # 设置后可以内网访问

    location / {  
        proxy_set_header X-Real-IP $remote_addr;  
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
        proxy_set_header Host $http_host;  
        proxy_set_header X-NginX-Proxy true;  
        proxy_pass http://127.0.0.1:5000/;
        proxy_redirect off;  
    }
}
```



在 `/usr/local/etc/nginx/nginx.conf` 中添加：

```
include /usr/local/etc/nginx/servers/FlaskUwsgiNginxDemo.conf;
```

启动 nginx

```
sudo nginx
```

重启（修改配置后经常需要）：

```
sudo nginx -s reload
```


# 其他

关闭 uwsgi:

```
sudo killall -9 uwsgi
```










参考：

- [http://codingpy.com/article/deploy-flask-app-on-cvm-with-nginx-uwsgi/](http://codingpy.com/article/deploy-flask-app-on-cvm-with-nginx-uwsgi/)

