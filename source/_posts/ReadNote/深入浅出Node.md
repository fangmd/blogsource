---
title: Node.js 深入浅出笔记
date: 2019-09-05 21:38:14
tags: [node]
categories: 读书笔记

---


# 第 1 章 Node 简介

## 1.1 Node 历程

1. 2009 5 月发布最初版本

## 1.2 why JavaScript

设计者认为, 设计高性能 web 服务器的要点: 事件驱动, 非阻塞 I/O

JavaScript 本身就是事件驱动的

## Node 给 JavaScript 带来的意义

![https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/node/chrome-node.jpg](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/node/chrome-node.jpg)

## 1.4 Node 的特点

1. 异步 I/O

异步调用：Don't call me, I will call you.

异步任务结束后，自动执行回调函数

2. 事件与回调函数

3. 单线程

单线程执行代码，不需要在意多线程中的状态同步问题，没有死锁的存在，也没有线程上下文交换带来的性能开销。

单线程弱点: 无法利用多核CPU; 错误会引起整个应用退出; 大量计算占用CPU导致无法继续调用异步I/O

**大量计算占用CPU问题处理方案:**

Google 采用 Web Workers 创建工作线程来执行计算任务。Node 使用类似思路 child_process 子进程处理大量计算任务。

通过 Master-Worker 管理各个工作进程.

4. 跨平台

Node 基于 libuv 实现跨平台。

## 1.5 Node 的应用场景

1. I/O 密集型
2. 与老后台系统共存
3. 分布式应用

# 第 2 章 模块机制

## 2.1 CommonJS 规范

涵盖内容：模块，二进制，Buffer，字符集编码，I/O 流，进程环境，文件系统，套接字，单元测试，web 服务器网关接口，包管理 等.

## 2.2 Node 模块实现

exports, require, module

模块分为: Node 提供的 核心模块；用户编写的 文件模块.

Node 引入模块需要经历的步骤:

1. 路径分析
2. 文件定位
3. 编译执行

**优先从缓存加载:** Node 会缓存编译执行后的模，便于再次使用。 优先级最高

>核心模块通常已经编译成二进制加入到源代码中，所以在使用的时候不需要 路径分析和文件定位 的步骤。

编译过程可以识别: `.js`, `.node`, `.json` 文件。

## 2.6 包与NPM

CommonJS 对包定义了规范。

### 包结构

1. package.json
2. bin: 可执行二进制文件
3. lib: js 源码
4. doc
5. test

# 第 3 章 异步I/O

## 3.1 为什么要异步

1. 用户体验

可以同时读取多个文件并减少等待时间。

后端可以快速响应资源，才能让前端的体验变好。

2. 资源分配

在执行多个不相关任务的时候。

单线程同步编程模型因阻塞I/O导致硬件资源得不到使用。多线程编程模型因为编程中的死锁，状态同步等问题提高编程难度。

Node 使用 单线程，异步I/O 更好执行多个不相关任务。

## 3.2 异步 I/O 实现现状

## 3.3 Node 的异步 I/O

### 3.3.1 事件循环

Node 执行模型：事件循环

一次循环称为: Tick

一次循环: 取出事件，处理回调(没有事件就 退出)

在不通的平台，事件循环实现方式不同: windows IOCP, *nix 基于多线程创建

### 3.3.2 观察者

Tick 过程中，使用 **观察者** 判断是否有事件需要处理

定时器会插入到观察者中，观察者内部数据结构是 红黑树。

### 3.3.3 请求对象

JS 将异步任务封装成一个请求对象，交给底层异步处理模块处理。

Windows 底层是 I/O 线程池中的线程去处理 异步任务。

### 3.3.4 执行回调

## 3.4 非 I/O 的异步 API

`setTimeout`, `setInterval()`, `setImmediate()`, `process.nextTick()`

### process.nextTick()

如果要立即异步执行一个任务:

```
setTimeout(function(){
  //TODO
}, 0);
```

这种方式浪费性能。

`process.nextTick` 内部实现是将回调函数放入队列(数组)，下一轮 Tick 时取出执行，不经过红黑树，更高效。

### setImmediate()

优先级:`process.nextTick()` > `setTimmediate()`

原因：事件循环观察者的检查有先后顺序。`process.nextTick()` 属于 idle 观察者，`setImmediate()` 属于 check 观察者。在每一轮检查中 idle 观察者 > I/O 观察者 > check 观察者

一个 Tick 中，`setImmediate()` 只会执行一个，另一个会等待下一个 Tick 执行

```
// 加入两个nextTick()的回调函数 
process.nextTick(function () {
  console.log('nextTick延迟执行1'); 
}); 

process.nextTick(function () {
  console.log('nextTick延迟执行2'); 
}); 

// 加入两个setImmediate()的回调函数 
setImmediate(function () { 
    console.log('setImmediate延迟执行1'); 
    // 进入下次循环 
    process.nextTick(function () {
        console.log('强势插入'); 
    }); 
}); 

setImmediate(function () {
  console.log('setImmediate延迟执行2'); 
}); 
console.log('正常执行');

其执行结果如下：

正常执行 
nextTick延迟执行1 
nextTick延迟执行2 
setImmediate延迟执行1 
强势插入 
setImmediate延迟执行2
```

## 3.5 事件驱动与高性能服务器


![https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/node/node-web-flow.jpg](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/node/ode-web-flow.jpg)


# 第 4 章 异步编程

## 4.1 函数式编程

1. 高阶函数

将函数作为返回值

例子: Array.forEach, map, reduce...

## 4.2 异步编程的优势与难点

难点:

1. 异常处理
2. 函数嵌套过深
3. 阻塞代码
4. 多线程编程

单一线程执行，在游览器中指 JavaScript 执行线程与UI渲染线程是同一个线程。

5. 异步转同步

## 4.3 异步编程解决方案

1. 事件发布/订阅模式
2. promise/Deferred 模式
3. 流程控制库

### 事件发布/订阅模式

发布/订阅模式: 事件监听器模式是一种广泛用于异步编程的模式，是回调函数的事件化。

```js
emitter.on('event1', function(msg){
  console.log(msg);
});

emitter.emit('event1', 'I am message!');
```

可以实现事件与多个回调函数关联，这些回调函数也称为 事件侦听器。

发布/订阅模式用于也常常用于解耦业务逻辑。

1. 继承 events。EventEmitter 类 实现发布/订阅模式

2. 利用事件队列解决雪崩问题

利用 `once` 过滤重复性事件。

> EventEmitter ，默认设置了监听器的数量限制，如果使用可能需要先去除限制。

3. 多异步之间的协作方案

如何实现并行调用，串行执行。

引入第三方函数和第三方变量处理异步协作的结果。 **哨兵变量**

结合闭包

### 4.3.2 Promise/Deferred 模式

```js
$.get('/api`, {
  success: onSuccess,
  error: onError,
  complete: onComplete
});

$.get('/api')
  .success(onSuccess)
  .error(onError)
  .complete(onComplete)
```

1. 通过 Deferred 对象可以处理多个业务逻辑。
2. 缓解嵌套

**Promises/A** 模式

### 4.3.3 流程控制库

1. 尾触发与 next

Node Connect 模块中使用了这种模式。

最简单的中间件:

```js
var app = connect();
// middleware
app.use(connect.staticCache());
app.listen(3001);


// other.js
function (req, res, next){
  // 中间件
}
```

2. async

最知名的流程控制模块 async.

**异步的串行执行:** `async.series()`

**异步的并行执行:** `async.parallel()` 同时拿到多个并行处理的结果

**异步调用的依赖处理:** `async.waterfall()` 

3. Step

## 4.4 异步并发控制

在使用异步的时候，要防止开启过多异步，防止文件系统进行大量并发调用。

1. bagpipe 解决方案

通过队列来控制并发量
如果当前活跃的异步并发量小于限定值，从队列中取出执行
如果活跃调用达到限定值，调用暂时存放在队列中
每个异步调用结束时，从队列中取出新的异步调用执行

2. async 解决方案

`async.parallelLimit()`

# 第 5 章  内存控制

## 5.1 V8 的垃圾回收机制与内存限制

由垃圾回收机制来进行自动内存管理。


1. V8 的内存限制

V8 限制了内存的用量，Node 通过 JavaScript 使用内存只能使用一部分内存(64 位系统 1.4GB, 32 位系统0.7GB)

2. V8 的对象分配

所有 JavaScript 对象都是通过堆来进行分配的。

**V8 限制了堆内存的大小，为什么？** 

堆内存过大，垃圾回收耗时太长。所以直接限制了堆内存的大小。

>可以通过 Node 启动时传递参数，调整内存限制 node --max-old-space-size=1024 test.js MB // node --max-new-space-size=1024 test.js MB

### V8 垃圾回收机制

各种垃圾回收算法:

1. 分代垃圾回收机制

对于不通代的垃圾使用不通的垃圾回收算法。

V8 将内存分为：新生代，老生代

2. Scavenge算法&Cheney算法回收老生代垃圾

Cheney 算法采用复制方法实现垃圾回收。(内存一分为二，一边使用一边不是用，回收的时候将不回收的移动到不使用的一边...)

3. Mark-Sweep 标记清除 & Mark-Compact 标记整理 回收新生代垃圾

4. Incremental Marking

避免 JavaScript 应用逻辑与垃圾回收器看到的情况不一致，在垃圾回收过程中将应用逻辑停止下来。全停顿(stop-the-world).

## 5.2 高效使用内存

1. 作用域

一个函数执行完成后，函数作用域内的变量也会销毁

2. 变量的主动释放

全局变量需要在进程退出时才能释放。

```js
delete global.foo // 使用 delete 释放内存

global.foo = undefined // or null, 使用赋值方法释放内存 (推荐)
```

3. 闭包

闭包形式下，中间函数将不会释放内存。

>正常 JavaScript 执行中，无法立即回收内存的有：闭包 和 全局变量

## 5.3 内存指标

1. 查看内存使用情况

```js
process.memoryUesage()
```

2. 查看系统的内存占用

```js
os.totalmem();
```

3. 堆外内存

堆内存总量 < 进程内存

## 5.4 内存泄露

通常原因:

1. 缓存

在使用缓存的时候应该注意: 

缓存限制策略：代码限制缓存大小，例子：限制数组长度；使用 LRU 算法缓存

使用进程外缓存: 例子:redis

2. 队列消费不及时

监控队列长度，报警

异步调用添加超时机制

3. 作用域未释放

## 5.5 内存泄露排查

使用第三方工具排查内存泄露

## 5.6 大内存应用

stream 操作大文件读写

# 第 6 章 理解 Buffer

操作字节

# 第 7 章 网络编程

网络模块: net, dgram, http, https 对应 TCP, UDP, HTTP, HTTPS

## 7.5 网络服务与安全

模块: crypto, tls, https


### TLS/SSL

非对称加密

使用公钥加密传输的数据，私钥解密。

在建立安全传输之前，服务器和客户端需要互换公钥。

客户端发送数据的时候，使用服务器公钥加密；服务器发送数据的时候使用客户端公钥加密。

**为了避免中间人攻击：**需要对公钥进行认证，确保公钥来自目标服务器。使用数字证书进行认证。

数字证书包含: 服务器名称，主机名，服务器公钥，签名发布机构名称，来自签名颁发机构的签名。

### 数字证书

CA: Certificate Authority 数字证书认证中心

# 第 8 章 构建 web 应用

# 第 9 章 玩转进程

Node 通过 child_process 模块实现，多进程创建，在单机上搭建 Node 集群。

## 9.3 集群稳定之路

1. 负载均衡

采用操作系统抢占式策略达到负载均衡

2. 状态共享

第三方数据存储：Redis
主动通知

## 9.4 Cluster 模块

Node v0.8 版本加入了 cluster 模块。 解决多核CPU利用率问题，比使用 child_process 更加简单

**工作原理：** 实际上是 child_process 和 net 模块的结合。

# 第 10 章 测试

# 第 11 章 产品化

## 11.1 项目工程化

项目的组织能力。

项目工程化最基本的几步:

1. 目录结构
2. 构建工具
3. 编码规范
4. 代码审查















