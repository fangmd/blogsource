---
title: Docker nginx
date: 2019-09-02 13:18:12
tags: [Docker, nginx]
category: Docker
---

https://github.com/fangmd/docker-examples

参考:[https://www.runoob.com/docker/docker-install-nginx.html](https://www.runoob.com/docker/docker-install-nginx.html)

# Download nginx

```
docker pull nginx
```

test:

```
docker run --name test-nginx -p 8081:80 -d nginx
```

运行 nginx 容器，映射电脑端口 8081 到容器的 80。打开 `http://127.0.0.1:8081` 可以看到 nginx 网页表示成功

查看正在运行的 容器

```
docker ps
```

# nginx 部署

创建需要的目录: `/nginx/www,  /nginx/logs, /nginx/conf`

将运行中的容器中的 nginx 配置文件复制到本地:

```
docker cp ec2accd6e8d5:/etc/nginx/nginx.conf ./nginx/conf
docker cp 0ad0541e2979:/usr/share/nginx/html ./nginx/www
```

配置 `docker-compose.yml`:

```
version: '2'
services:
  nginx:
    image: nginx:stable-alpine
    restart: unless-stopped
    volumes:
      - ./nginx/conf/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/conf.d:/etc/nginx/conf.d/
      - ./nginx/www:/usr/share/nginx/html
      - ./nginx/logs:/var/log/nginx
    ports:
      - '8082:80'
    # environment:
    #   - NGINX_HOST=your.domain
    #   - NGINX_PORT=80

```

运行：

```
docker-compose up -d --build
```

# Dockerfile 方式


Dockerfile:

```
FROM nginx

COPY ./nginx/www /usr/share/nginx/html
```

根据 Dockerfile 创建镜像:

```
docker build -t one-nginx .
```

运行:

```
sudo docker run --name one-nginx -d -p 8080:80 nginx
```

打开: `http://127.0.0.1:8080`

