---
title: JS 知识点
date: 2019-08-22 13:18:12
tags: [js]
category: 前端
---

# 继承与原型链

JS 中只有一个结构: 对象。

每个对象又一个私有属性: `__proto__` 指向它构造函数中的原型对象 `prototype`

当一个对象的 `__proto__ === null` 时，就是原型链的最后一个节点

原型链的顶端一般都是 `Object`

## 如何实现一个继承 & 如何创建对象

### new ， 构造器

```js
function Man() {
  this.name = 'fang';
}

Man.prototype = {
  color: 'white',
};

var m = new Man();
```

### Object.create

```
var p = Object.create(o); // 表示 p extends o
```

`Object.create` 源码(简化了):

```js
Object.create = function(proto, propertiesObject) {
  // 省略前置 参数 判断...

  function F() {}

  F.prototype = proto;
  return new F();
};
```

### class 关键字创建对象

class, constructor, static, extends, super

# 严格模式

ES5 开始引入 严格模式

作用:

1. 将过失错误转化成异常
2. 一些非法操作异常报错
3. 为未来 js 语法预留关键字(使用一些关键字会报错)

## 开启严格模式

开启严格模式:

```
"use strict"
```

可以在函数内部开启严格模式:

```js
function strict() {
  // 函数级别严格模式语法
  'use strict';
  function nested() {
    return 'And so am I!';
  }
  return "Hi!  I'm a strict mode function!  " + nested();
}
```

# JS 内存管理

内存生命周期：

1. 分配内存
2. 使用内存
3. 释放内存

## 分配内存

JS 在定义变量的时候自动完成内存分配

## 使用内存

对变量进行操作

## 释放内存

垃圾回收器：跟踪内存的分配和使用，当内存不再使用时自动释放它。

### 垃圾回收算法

1. 引用计数垃圾收集

缺点：循环引用无法处理

2. 标记-清除算法

root 对象，向下查找对象，没有查找到的就是需要回收的对象

优点：解决循环引用问题
缺点：那些无法从根对象查询到的对象都将被清除

# JS 并发模型与事件循环

JS 运行时：Stack(栈), 堆(Heap)，队列(Queue)

## Stack

函数调用形成栈帧

函数执行过程中的局部变量存储在帧中。

## 堆 Heap

对象被分配在一个堆中

## 队列

JS 运行时包含一个待处理的消息队列。

事件循环队列伪代码:

```js
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

> `queue.waitForMessage` 阻塞

1. 执行至完成

一个消息完整执行前，其他消息不会被执行。

2. 添加消息

一个事件发生并且有监听器绑定在事件上时，这个事件消息就会加入到消息队列中，否则 事件丢失。

3. 零延迟

零延迟并不意味着回调会立即执行。

## 多个运行时通讯

iframe 有自己的运行时

运行时之间通过 `postMessage` 通讯

## 永不阻塞

js 事件循环模型，在处理 I/O 通过事件和回调实现。

在 I/O 过程中并不会阻塞事件处理。

也就是说在网络请求过程中，可以处理其他事件。

# JS 相等性判断 ==, ===, Object.is

1. 严格相等 `===`
2. 宽松相等 `==`
3. `Object.is` ES6 与严格相等表现一样，增加:`NaN与NaN相等， -0与+0不等`

# 闭包

## 是什么

嵌套函数可以访问外部声明的变量。

```js
function makeFunc() {
  var name = 'Mozilla';
  function displayName() {
    alert(name);
  }
  return displayName;
}

var myFunc = makeFunc();
myFunc();
```

JavaScript 中函数会形成闭包。闭包由函数和创建改函数的词法环境组合而成。

什么是闭包？

> JavaScript 函数内部嵌套函数就会形成闭包，闭包由内部函数和创建函数的词法环境组成。

## 作用

1. 延迟执行: 通过返回内部函数的形式达到延迟执行的作用，创建函数和执行函数分离
2. 变量与外部环境隔离

## JQuery 闭包

```js
(function() {
  // jquery code
})();
```

> 匿名函数自执行

```js
//以下截取自jquery源码片段
(function(window, undefined) {
  /*    源码内容    */

  window.jQuery = window.$ = jQuery;
})(window);
```

# 原生 DOM 操作几种方式

`Element` 对象的几个方法

1. 替换 `replaceChild`
2. 删除 `removeChild`
3. 插入 `insertBefore, appendChild`

# 深拷贝和浅拷贝

js 数据类型

1. 基本数据类型: undefined, boolean, number, string, null
2. 引用数据类型:

> 深/浅拷贝都是对于引用对象来说的

**浅拷贝: ** 拷贝后的对象，成员变量指向和原来的对象成员变量指向同一个内存地址。

> 浅拷贝，对象的属性只能拷贝基本数据类型

```js
function shallowCopy(target) {
  var obj = {};
  for (let key in target) {
    obj[key] = target[key];
  }
  return obj;
}
```

**深拷贝: **生成的对象完全独立与原来的对象。

```js
function deepCopy(target){
  let obj = {};
  for(let key in target){
    if( typeof target[key] === 'object'{
      obj[key] = deepCopy(target[key]);
    }else{
      obj[key] = target[key];
    }
  }
  return obj;
}

方式二: JSON

let obj2 = JSON.parse(JSON.stringify(obj));
```

## obj.keys vs obj[keys]

两个的作用是一样的。

但是 `obj.keys` 只能用于 obj 中已经定义好的 key(需要是在代码未运行就定义好的key, 不能是动态添加的key)).

# Event Loop

Event Loop: JS 引擎线程会循环从任务队列中读取事件并执行

没有游览器环境只有一个 Event Loop;

一个 Event Loop 有1个或多个 task queue

```
代码执行顺序:

同步代码
消息队列中异步代码
回调函数
下个事件中执行 setTimeout
全局上下文进入函数调用栈
micro-task 微任务
macro-task 宏任务
```

>setTimeout 最短执行时间是 4ms

**micro-task 微任务队列** `Promise MutationObserver`

**macro-task 宏任务队列** `setTimeout setInmediate MessageChannel`


# Promise

