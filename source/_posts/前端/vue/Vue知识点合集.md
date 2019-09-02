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

