---
title: 你不知道的JS(下) 笔记
date: 2020-01-04 11:20:16
tags: [JavaScript]
categories: 读书笔记

---

**第一部分 起步上路**

# 第1章 深入编程

# 第2章 深入 JavaScript

# 第3章 深入“你不知道的 JavaScript”

>上面的内容都很基础

**第二部分 ES6 及更新版本**

1. 2009年 ES5
2. 开发者想用新语法，但是一些运行环境不支持新语法，于是有了 transpiling(转换+编译) 技术，将新语法转化成低级的语法。比如 Babel 工具
3. 一些新的 API 可以使用 shim/polyfill 扩充
4. 箭头函数：除了更短的语法外，会影响 this
5. ES6 增加了 for...of
6. ES6 增加 Map, Set
7. ES6 新增加 API

```js
// Array:
var arr = []
Array.of(...)
Array.copyWithin(...)
arr.entries()
arr.values()
arr.keys()

//Object
Object.assign(...)
```

>后面不用看


