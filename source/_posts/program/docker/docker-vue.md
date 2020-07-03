---
title: Docker Vue
date: 2020-07-03 13:18:12
tags: [Docker]
category: Docker
---


使用 docker 部署 vue

参考：

[https://juejin.im/post/5cce4b1cf265da0373719819#heading-23](https://juejin.im/post/5cce4b1cf265da0373719819#heading-23)


# 方式三：推荐

[https://cn.vuejs.org/v2/cookbook/dockerize-vuejs-app.html](https://cn.vuejs.org/v2/cookbook/dockerize-vuejs-app.html)

## 创建文件: `dockerfile`:

```
# build stage
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# production stage
FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```


## 创建文件: `docker.sh`:

```
#!/bin/bash

echo "start run vue"

echo "git pull code start"

git pull

echo "Stop/Delete old container"

docker stop dockerize-vuejs-app-1

echo "create docker image"

docker build -t vuejs-cookbook/dockerize-vuejs-app .

echo "run docker container"

# docker run -it -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app

docker run -it -d -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

## 运行

```
chmod +x docker.sh

./docker.sh
```

>每次代码更新后执行: ./docker.sh




# 方式二: dist 文件挂载到 Container

`./nginx/default.conf`

```js
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;

    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    gzip on;
    gzip_static on; 
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ssi on;
    
}
```

## docker.sh 运行脚本

`docker.sh`

```shell
#!/bin/bash

echo "start npm run install & npm run build"

npm install & npm run build

container=$(docker container ls -a | grep vuenginxnew)
if [ -n "$container" ]; then
    echo "container is existed, restart container"
    docker stop vuenginxnew
    docker start vuenginxnew 
else
    echo "container not exist , create & start container success"
    docker run \
    -p 3000:80 \
    -d --name vuenginxnew \
    --mount type=bind,source=/Users/double/webWorkSpace/pvue/nginx,target=/etc/nginx/conf.d \
    --mount type=bind,source=/Users/double/webWorkSpace/pvue/dist,target=/usr/share/nginx/html \
    nginx
fi
```

>每次 git 拉完代码后执行 ./docker.sh 就 ok


# 方式一 使用 Dockerfile 构建镜像

>缺点：每次更新代码都需要删除旧的镜像和容器


```
# Dockerfile

FROM nginx

COPY nginx/default.conf /etc/nginx/conf.d/default.conf
```

`./nginx/default.conf`

```js
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;

    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    gzip on;
    gzip_static on; 
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ssi on;
    
}
```

创建镜像:

```
docker build -t vuecontainer .
```

查看镜像:

```
docker images
```

运行镜像:

```
docker run \
-p 3000:80 \
-d --name vuenginxnew \
--mount type=bind,source=/Users/double/webWorkSpace/pvue/nginx,target=/etc/nginx/conf.d \
--mount type=bind,source=/Users/double/webWorkSpace/pvue/dist,target=/usr/share/nginx/html \
nginx
```


>-d 后台运行， --name:容器名称, -p 映射端口

查看运行情况:

```
docker ps
```

访问: `http://localhost`

## 问题

这种方式使用 docker, 在代码修改后，重新部署的时候，需要重启容器，才会生效。



