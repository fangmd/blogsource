---
title: 使用 virtualenv 建立独立的开发环境
date: 2017-01-05 11:38:14
tags: [virtualenv]
categories: Linux

---


 virtualenv 可以搭建虚拟且独立的python环境，可以使每个项目环境与其他项目独立开来，保持环境的干净，解决包冲突问题。

 # Install

 ```
 pip install virtualenv
 ```

# Usage

## 创建一个环境：

<!--more-->

```
virtualenv [File Name]
```

生成一个文件夹，名称自己制定比如 ENV: 内部文件目录结构：

- `ENV/lib/`
- `ENV/lib/`


## 启动这个环境

```
source bin/activate
```

启动后命令行的抬头会改变，比如：
```
(ENV) DoubledeMacBook-Pro:bin double$
```

## 关闭环境

```
deactivate
```

# 在虚拟环境内部切换 python 版本

在外部安装了 python2 版本和 python3 的时候，可能会需要改变虚拟环境的 python 版本

```
virtualenv -p /usr/bin/python2.6 <path/to/new/virtualenv/>
```

example:

```
virtualenv -p /usr/bin/python3 ~/temp/ENV/

virtualenv -p /usr/local/bin/python3 ~/temp/ENV/
```