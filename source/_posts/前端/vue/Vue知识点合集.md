---
title: Vue-响应式原理
date: 2019-02-27 13:18:12
tags: [vue]
category: 前端

---


# 父子组间交互方式

## Vuex

## props/$emit

## sync 实现数据双向绑定

```js
// 父组间使用时
<custome-view :show.sync="showCustomeView">

//子组件
props:{
  show: false,
}

// 子组间修改值
this.$emit('update:show', false);
```

## $attrs/$listeners

## provide/inject

父组间 provide 属性获取函数

子组间 inject 注入父组间提供的属性和函数

## $parent/$children/$root/$refs

缺点：多级访问麻烦

# MVVM 实现原理，双向绑定原理

## Vue2.0

通过数据劫持结合发布者-订阅者模式的方式实现。

1. 实现一个监听器 Observer, 用来劫持并监听所有属性，有变动就通知订阅者 Watcher

```
通过 Object.defineProperty() 实现对属性 set 方法的拦截
```

2. 实现一个订阅者 Watcher, Watcher 绑定一个更新函数，收到属性变化后更新视图

```
直接修改 VDOM
```

3. 实现一个解析器，扫描和解析每个节点的相关指令. 初始化节点上的模版数据，创建对应的 Watcher


## Vue3.0

```
Proxy 实现代理
```


