---
title: Docker Learn
date: 2017-01-01 13:18:12
tags: [Docker]
category: Docker

---

# Get Started with Docker

## Build your own image

### Write a Dockerfile

```
$ mkdir mydockerbuild

$ cd mydockerbuild

$ view Dockerfile
```

`Dockerfile` 文件中先添加下面的内容：表示这个 image 基于 `docker/whalesay` image 

```
FROM docker/whalesay:latest
```

安装 `fortunes`:

```
RUN apt-get -y update && apt-get install -y fortunes
```

`CMD` 表示要执行的命令

```
CMD /usr/games/fortune -a | cowsay
```

### Build an image from your Dockerfile

编译 docker image， `-t [image name]` 表示命名这个 image， `.` 表示从当前目录找 DockerFile
```
docker build -t docker-whale .
```

### Learn about the build process

编译命名执行后：会读取 DockerFile 文件，执行里面的语句

1. Docker 检查 DockerFile 的完整性，输出语句：

        Sending build context to Docker daemon 2.048 kB

2. 执行语句：检查本地是否有 `whalwsay` image， 如果没有就从 Docker HUB pull 

        Step 1 : FROM docker/whalesay:latest
        ---> 6b362a9f73eb

    最后输出 `whalwsay` 的 ID

3. 执行语句：

    Docker 创建临时的 container 执行下面的命名，安装 `fortunes` 软件
        Step 2 : RUN apt-get -y update && apt-get install -y fortunes
    安装结束后临时 container 会被移除

4. 执行语句：

    创建新的中间 container a8e6faa88df3 执行 CMD 命令。
        Step 3 : CMD /usr/games/fortune -a | cowsay
        ---> Running in a8e6faa88df3
        ---> 7d9495d03763
        Removing intermediate container a8e6faa88df3
        Successfully built 7d9495d03763

### Run your new docker-whale

```
docker images
```

```
docker run docker-whale
```

## Tag, push, and pull your image

### Tag and push the image

```
docker tag [id] [account name/image name:latest]
docker tag 7d9495d03763 maryatdocker/docker-whale:latest
```

```
docker login
```

```
docker push [image tag]
```

### Pull your new image

删除 本地 镜像

```
docker rmi -f 7d9495d03763
```

直接运行镜像，就会从远程下载镜像

```
docker run fangmingdong/learn
```

# Learn by Example

## Hello world in container

### Run a Hello world

```
docker run ubuntu /bin/echo 'Hello world'
```

- `docker run` 运行一个容器
- `ubuntu` 是一个 镜像
- `/bin/echo 'Hello world'` 命令运行在内部的一个新容器中


### Run an interactive container

```
docker run -t -i ubuntu /bin/bash
```

- `-t`表示在容器内部使用终端
- `-i` 允许和容器建立外部连接

### Start a daemonized Hello world

```
docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

- `-d` flag runs the container in the background (to daemonize it).

运行后输出: container id

```
10331d6d3d678d2400d09b7a570fb07faca75db1e74c764cd503f56cb372670a
```

使用 `docker ps` 查看正在运行的 container

```
CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES
1e5535038e28  ubuntu  /bin/sh -c 'while tr  2 minutes ago  Up 1 minute        insane_babbage
```

这里的 container id 是缩写.

```
docker logs insane_babbage
```

- `docker logs` 查看容器内部


停止容器运行

```
docker stop insane_babbage
```

## Run a simple application

### Learn about the Docker client

Docker 程序就是 Docker 客户端

通过命令行的形式使用 Docker 客户端

```
[sudo] docker [subcommand] [flags] [arguments] ..
```

### Get Docker command help

```
docker --help
```

具体命令使用帮助：

```
docker attach --help
```

### Run a web application in Docker

下面的例子运行：Python Flask application

```
docker run -d -P training/webapp python app.py
```

- `-P`:表示映射所有端口到容器内部
- `training/webapp`:是一个预先创建好的 image
- `python app.py`: 这个命名运行在容器内部，启动 web 应用

### View the web application container

```
docker ps -l
```

查看刚才运行的 container

- `-l`: 表示最后运行的 container

查看 docker-machine ip:

```
docker-machine ip
```

### A network port shortcut

```
docker port nostalgic_morse 5000
```

### View the web application’s logs

```
$ docker logs -f nostalgic_morse

* Running on http://0.0.0.0:5000/
10.0.2.2 - - [06/Nov/2016 20:16:31] "GET / HTTP/1.1" 200 -
10.0.2.2 - - [06/Nov/2016 20:16:31] "GET /favicon.ico HTTP/1.1" 404 -
```

### Look at the web application container’s processes

```
$ docker top nostalgic_morse

PID                 USER                COMMAND
854                 root                python app.py
```

### Inspect the web application container

```
$ docker inspect nostalgic_morse

$ docker inspect [container name]
```
### Stop the web application container

```
docker stop [container name or container id]
```

### Restart the web application container

```
docker start [container name or container id]
```

### Remove the web application container

```
docker rm [container name]
```

## Build your own images

## Network containers

## Manage data in containers

# Docker Compose

## Overview of Docker Compose

Docker Compose 是一个可以运行多个 container 的 Docker 工具应用。

使用 Compose file 配置应用的服务，然后使用一个命名就能启动所有的服务。

使用 Compose 三个步骤：

- 使用 Dockerfile 定义 Docker app 的运行环境
- 使用 `docker-compose.yml` 定义所有需要启动的服务，然后他们可以一起启动
- 最后运行 docker-compose


`docker-compose.yml` 例子：

```
version: '2'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

Compose 有命名自己的命令工具控制整个应用的生命周期：

- Start, stop and rebuild services
- View the status of running services
- Stream the log output of running services
- Run a one-off command on a service

## Get started with Docker Compose


## Quickstart: Compose and Django

### Define the project components

1. 创建一个工程目录 `dockerdjango`
2. 目录中创建 `Dockerfile`
        
        FROM python:3.5.2
        ENV PYTHONUNBUFFERED 1
        RUN mkdir /code
        WORKDIR /code
        ADD requirements.txt /code/
        RUN pip install -r requirements.txt
        ADD . /code/
3. 目录中创建 `requirements.txt`

        Django
        psycopg2

4. 目录中创建 `docker-compose.yml`

        version: '2'
        services:
          db:
            image: postgres
          web:
            build: .
            command: python manage.py runserver 0.0.0.0:8000
            volumes:
              * .:/code
            ports:
              * "8000:8000"
            depends_on:
              * db
              
    这里建立了两个服务：db，web

### Create a Django project

1. 切换工作目录到工程根目录
2. 使用 `docker-compose` 命令创建 Django 工程

    ```
    docker-compose run web django-admin.py startproject composeexample .
    ```

    上面的语句运行 `django-admin.py startproject composeexample` 命令在 container 内部，使用 `web` service 的 image 和 configuration。如果是初次运行会在当前目录创建 `web` image 因为在 `docker-compose.yml` 中定义了 `build: .`
3. 

### Connect the database

`composeexample/settings.py` file:

```
DATABASES = {
     'default': {
         'ENGINE': 'django.db.backends.postgresql',
         'NAME': 'postgres',
         'USER': 'postgres',
         'HOST': 'db',
         'PORT': 5432,
     }
 }
```


```
docker-compose up
```

访问 `http:docker-machone ip:8000` 查看是否运行成功


