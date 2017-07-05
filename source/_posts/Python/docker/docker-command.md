---
title: Docker Command
date: 2017-01-01 13:18:12
tags: [Docker]
category: Docker

---


command: python blog/manage.py runserver 0.0.0.0:8000

/etc/nginx/logs/error.log

getting INI configuration from blog/myconf.ini

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

