---
title: npm
date: 2019-01-11 13:18:12
tags: [npm]
category: 前端

---

# npm 升级 npm

```
npm install -g npm
```

# npm 查看当前项目依赖情况

```
npm ls
```

# 查看 npm global packages

```
npm list -g --depth 0

npm uninstall -g [package-name]
```


# npm init

在创建一个新的 node 工程并且希望使用 npm 管理 js 所有依赖库的使用，会使用 npm init 初始化工程.

>在实际开发中，不会直接使用 npm init, 比如在 react 开发的时候使用 create-react-app 脚手架，它可以达到 npm init 的效果同时可以自动创建其他需要的工程文件.

`npm init`: 实际作用是帮助我们生成 `package,json`

```json
{
  "name": "test",
  "version": "1.0.0",
  "description": "test desc",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

这个文件用于描述工程，记录工程需要的依赖库。

# npm install [package-name]

>执行 npm install xxxx 在不带版本好的时候，会自动下载最新版本(如果本地存在这个 pacakge, 会自动更新这个包)

`npm install` 安装 `package,json` 文件描述的依赖包，依赖包下载后放在 `node_modules` 文件夹中，同时会生成 `package-lock.json` 文件。

`npm install [package-name]` 安装某个依赖包，并添加到 `package,json` 中。

## npm install -g [pacakge-name]

>执行 npm install -g xxxx 在不带版本好的时候，会自动下载最新版本(如果本地存在这个 pacakge, 会自动更新这个包)

全局安装，通常安装依赖都是对于某个工程而言的。

如果需要安装依赖到全局可以使用 `npm install -g [pacakge-name]`

全局安装

1. 将安装包放在 /usr/local 下
2. 可以直接在命令行里使用





