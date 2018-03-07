---
title: Docker Command
date: 2017-01-01 13:18:12
tags: [Docker]
category: Docker

---

# 重新整理

# 基本

查看 docker 的信息：

```
docker info
```

查看 docker 占用的硬盘大小：

```
docker system df
```





# 镜像相关

下载镜像：

```
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]

docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

运行镜像:

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```



```
docker image COMMAND

Manage images

Options:
      --help   Print usage

Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
```

```
# 删除不再使用的镜像，比如：none 类型的镜像
docker image prune 
```

列出镜像：

```
# 列出仓库是 ubuntu 的镜像
docker image ls ubuntu

# 列出 指定仓库名和标签
docker image ls ubuntu:16.04

# image ls 支持 过滤
docker image ls -f since=mongo:3.2
docker image ls -f label=com.example.version=0.1
```

删除镜像：可以使用 长ID，短ID（前3位或者更长），镜像名

```
docker image rm [选项] <镜像1> [<镜像2> ...]

docker image rm 501
docker image rm centos

# 配合 ls 删除镜像
docker image rm $(docker image ls -q redis)
```

## 镜像修改

- commit    （禁止使用）
- Dockerfile：推荐

## Dockerfile 构建镜像

```
docker build [选项] <上下文路径/URL/->

docker build -t nginx:v3 .
```

# Dockerfile 语法

## FROM

`FROM`: 指定基础镜像，必备指令，且必须是第一行。

```
FROM nginx
```

空镜像为基础：一些使用 Go 开发的应用可能会以空镜像为基础

```
FROM scratch
```

## RUN

`RUN`： 执行命令

- shell 格式

```
RUN <命令>

RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

- exec 格式

```
RUN ["可执行文件", "参数1", "参数2"]

RUN apt-get update
```











---


# docker ps

进程处理，查看

```
docker ps --help

docker ps -l

docker kill [CONTAINER ID]
docker rm [CONTAINER ID]
```


# docker images

查看 docker 中的镜像

# tag 
给 Image 命名

```
docker tag [id] [new image tag]
docker tag [id] [account name/image tag:latest]
```

# login

```
docker login
```

# push
```
docker push [image tag]
```

# rm image
```
docker rmi -f [image tag or image id]
```

# remove container
```
docker rm [container name]
```

# stop container 

```
docker stop [container id or names]
```

# restart container
```
docker start [container name]
```

# 查看 docker machine ip
```
docker-machine ip
```


# docker compose

## start compose
```
docker-compose up
```

## update code

```
docker-compose build
```

## 停止 compose

```
docker-compose ps

docker-compose rm -all
```

# SEE FILE IN CONTAINER

```
docker export dockerblog_web_1 > contents.tar
```

