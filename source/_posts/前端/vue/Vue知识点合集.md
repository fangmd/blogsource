---
title: Vue-响应式原理
date: 2019-02-27 13:18:12
tags: [vue]
category: 前端

---

# 页面之间传值

1. `params`

```js
路由配置:
{
    path: '/two/:id',
    name: 'two',
    component: () => import('@/components/two.vue'),
    meta: {
        title: 'two'
    }
}

> path 中也可以不定义 id，但是这样 params 在刷新后就会丢失

跳转:

this.$router.push({ path: `/two/${item}` })
this.$router.push(`/two/${item}`)
this.$router.push({ name: 'two', params: { id: `${item}` }})
```

2. `query`

```js
// 不需要在 vue-router 配置中设置。

this.$router.push({
    path: `/two`,
    query: {
        id:item
    }
})
// 参数会体现在地址栏中
```

# this.$router & this.$route

1. `this.$router` 是 VueRouter 实例，用于导航跳转
2. `this.$roue` 是当前 router 跳转对象，里面包含了 `name, path. query. params`




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

# Virtual DOM

使用 JS 对象模拟 DOM，vue 页面修改的时候先修改 vdom, 然后通过 diff 算法生成新 vdom, 最后渲染。

优点：

1. 性能好，效率高
2. 容易实现跨平台

# 异步更新 DOM, 以及 nextTick

vue 采用异步执行 DOM 更新。

一个数据修改，Watcher 生成一个事件插入队列，会在下一个事件循环中处理事件，更新界面。

nextTick: 实现将函数存入 microTask 或 macroTask。

**为什么使用异步更新DOM:** 优化性能，避免短时间内某个值一直改变导致渲染频繁。

在一些需要在数据变更后马上执行的代码需要使用 `nextTick`;









