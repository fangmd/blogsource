---
title: Vue-响应式原理
date: 2019-02-27 13:18:12
tags: [vue]
category: 前端

---

# 数据变化和组件如何关联

1. vue 如何获取 data 中的数据(Vue 双向绑定的原理)

Vue 双向绑定是由数据劫持结合发布者-订阅者模式实现的。

数据劫持：Vue 会遍历 data 函数返回的对象的所有属性，使用 Object.defineProperty 劫持对象属性的 setter/getter, 在这些函数被调用的时候插入代码。

> <=IE8 不支持 Object.defineProperty，所以不支持 Vue

劫持例子：

```js
//在console.log(book.name)同时,直接给书加一个书号var Book = {};var name = '';
Object.defineProperty(Book,'name',{    set:function(value) {
        name = value;
        console.log('你取了一个书名叫:'+value);
    },    get:function() {
        console.log('get方法被监听到');        return '<'+name+'>';
    }
});
```

发布者-订阅者(Observer-Watcher)：两个步骤：初始化发布者，订阅者；订阅者注册法到发布者，发布者发消息的时候依次向订阅者发布消息。








data 中的数据对象是 `Observer`




2. 如何监听 data 中数据对象的变化

每个组件都有一个 watcher 对象监听变化，data 数据对象中通过 setter 修改数据后, 通知 watcher 更新组件

![https://cn.vuejs.org/images/data.png](https://cn.vuejs.org/images/data.png)

3. 组件异步更新队列

组件异步更新队列有去重功能。




# 注意事项

1. 受 JavaScript 的限制，data 中的数据对象属性不能动态添加。










参考：

- [官方文档](https://cn.vuejs.org/v2/guide/reactivity.html)