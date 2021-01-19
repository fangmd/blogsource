---
title: NuxtJS 集成 Bootstrap
date: 2020-01-06 11:58:18
tags: [NuxtJs, Bootstrap]
categories: NuxtJS
---

# 前言

目前网络上的文章都是写的集成 `BootstrapVue` 的方式。

这篇文章介绍 NuxtJs 如何集成 Bootstrap

# 安装

```
npm i bootstrap
```

>写文章时 bootstrap 版本是："bootstrap": "^5.0.0-beta1",

复制 Bootstrap JS 文件到 `static/js` 下:

```
cp node_modules/bootstrap/dist/js/bootstrap.bundle.min.js static/js/bootstrap.bundle.min.js
```

修改 `nuxt.config.js`: 添加 `bootstrap.css`, `bootstrap.js`

```js
import path from 'path'

export default {
  // Global page headers (https://go.nuxtjs.dev/config-head)
  head: {
    // ...
    script: [
      {
        src: '/js/bootstrap.bundle.min.js',
        body: true,
      },
    ],
  },

  //...

  // Global CSS (https://go.nuxtjs.dev/config-css) 先后顺序会影响 css 优先级
  css: [
    path.resolve(__dirname, 'node_modules/bootstrap/dist/css/bootstrap.css'),
    // ...
  ],
}
```
