---
title: 后台开发慢？教你自己模拟API
date: 2017-01-12 08:18:12
tags: [Charles, API]
category: program

---

是否还在苦恼后台的兄弟开发慢，导致开发无法进行？

下面介绍在本地模拟 API 的方式

# 使用 Charles 模拟 API

前提：安装好 Charles，并且抓包功能正常运行。

## 步骤1
在网页中打开一个需要模拟的 API 地址，比如：`http://123.123.123.123/haha`

可以看到 Charles 中：

![Charles-mock-api0](https://github.com/fangmd/markdownphoto/raw/master/src/charles-mock-api0.png)

## 步骤2 建立需要的请求返回结果

<!--more-->

`/Users/double/Desktop/apidemo`:

```json
{
    "result": { 本地 API 文件
        "songs": [{
            "rtUrls": [],
            "ar": [{
                "id": 1000
                "name": "Shawee"
            }, {
                "id": 896274,
                "name": "mAjorHon"
            }],
            "al": {
                "id": 35091382,
                "name": "简单点 (Easier)",
                "pic_str": "109951162832212843",
                "pic": 109951162832212843
            },
            "st": 0,
....
```

## 步骤2 设置 Map Local

在 Charles 目标请求中 `右击` 选择 `Map Local`:

![Charles-mock-api1](https://github.com/fangmd/markdownphoto/raw/master/src/charles-mock-api1.png)


出现窗口设置 API：

![Charles-mock-api2](https://github.com/fangmd/markdownphoto/raw/master/src/charles-mock-api2.png)

在上图， `Map to Local path` 中设置本地的 API 文件（json数据），点击 OK

网页请求 `http://123.123.123.123/haha` 就会返回 `apidemo` 中的数据

最后管理设置的 Map Local API：

![Charles-mock-api3](https://github.com/fangmd/markdownphoto/raw/master/src/charles-mock-api3.png)

# 使用 json server

[https://github.com/typicode/json-server](https://github.com/typicode/json-server)


json server 可视化界面：[https://github.com/naholyr/json-server-gui](https://github.com/naholyr/json-server-gui)

参考：

- [http://www.wangyuwei.me/2017/01/04/%E6%89%8B%E6%91%B8%E6%89%8B%E5%B8%A6%E4%BD%A0mock-api/](http://www.wangyuwei.me/2017/01/04/%E6%89%8B%E6%91%B8%E6%89%8B%E5%B8%A6%E4%BD%A0mock-api/)





