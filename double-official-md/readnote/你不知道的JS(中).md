---
title: 你不知道的JS(中) 笔记
date: 2020-01-04 11:20:16
tags: [JavaScript]
categories: 读书笔记

---

**第一部分 类型和语法**

# 类型

1. 内置类型 7 种

`null, undefined, boolean, number, string, object, symbol(ES6 新增)`

>可以使用 typeof 确认变量的值的类型，但是 null 的 typeof 是 object.
>函数的 typeof 是 function

2. 变量没有类型，变量指向的值才有类型

# 值

1. JS 中没有真正意义上的整型

JS 数字类型基于 IEEE 754 标准实现，“浮点数”。JS 使用双精度格式 (64位二进制)

```js
// 无效语法:
42.toFixed( 3 ); // SyntaxError
// 下面的语法都有效: 
(42).toFixed( 3 ); // "42.000" 
0.42.toFixed( 3 ); // "0.420" 
42..toFixed( 3 ); // "42.000"
```

2. 整形检测

```js
Number.isInteger(xxx)

// ES6 之前的 polyfill
if (!Number.isInteger) {
         Number.isInteger = function(num) {
             return typeof num == "number" && num % 1 == 0;
         };
}
```

3. 不是数字的数字 `NaN`

NaN 与自身不相等

可以使用 `Number.isNaN(...)`  判断一个数是否是 `NaN`

4. 无穷数 `Infinity, -Infinity`

5. 值和引用

简单值就是传值，复合值(对象)就是传引用

数组浅拷贝: `var b = a.slice()`

封装的基本类型值对象(Number String ...)，还是传的值。

# 原生函数

>JavaScript 为基本数据类型值提供了封装对象，称为原生函数(如 String、Number、Boolean 等)。

`String(), Number(), Boolean(), Array(), Object(), Function(), RegExp(), Date(), Error(), Symbol()`

1. 内部属性，typeof 属于 object 的对象，都包含一个属性: `Class`

查看 `Class` 属性: 

```js
Object.prototype.toString.call([1,2,3])
// '[object Array]'
```

2. 封装对象包装

拆封： `xx.valueOf()`

# 强制类型转换

分为：隐式强制类型转换，显式强制类型转换

1. 显式强制类型转换

`toString(), toNumber()`

`parseInt(..), parseFloat(..)`

`!!xxx`

2. 隐式强制类型转换

字符串和数字直接的隐私转换:

```js
var a = 'a';
var b = 12
var c = a + b; // 'a12'
```

宽松相等和严格相等: 宽松相等允许进行强制类型转换，严格相等不允许强制类型转换

>错误/不准确的认知：== 是检查值相等，=== 检查值和类型相等

# 语法

1. 语句和表达式

语句都有结果值，在控制台直接执行语句可以看到结果值(undefined 或者 其他)

2. 错误

TypeError, ReferenceError, SyntaxError


**第二部分 异步和性能**

# 异步：现在与将来

1. `console.*` 实际上是异步执行的
2. 事件循环

JS引擎本身没有异步概念，需要外部的 事件调度 工具实现异步功能。

`setTimeout(...)` 是在一定时间后，把事件加入到事件队列中，等待执行。

程序被分成很多个小块，小块在事件队列中等待执行。

用户交互、IO、定时器会向事件队列中加入事件。

3. 并发

非交互：多个事件，事件的处理顺序不会影响结果

交互：多个事件，事件的处理顺序影响最终结果

协作：JS 单线程，如果出现大量的JS代码执行会让页面卡顿，可以通过事件循环机制让一部分任务延后处理，让引擎先处理UI代码

```js
var res = [];
// response(..)从Ajax调用中取得结果数组 
function response(data) {
    // 一次处理1000个
    var chunk = data.splice( 0, 1000 );
    // 添加到已有的res组 
    res = res.concat(
        // 创建一个新的数组把chunk中所有值加倍 
        chunk.map( function(val){
            return val * 2; 
        })
    );
    // 还有剩下的需要处理吗? 
    if (data.length > 0) {
          // 异步调度下一次批处理 
        setTimeout( function(){
                 response( data );
        }, 0 );
    } 
}
// ajax(..)是某个库中提供的某个Ajax函数 
ajax( "http://some.url.1", response ); 
ajax( "http://some.url.2", response );
```

4. 任务

ES6中， 在事件队列(tick)的基础上增加一个 任务队列(微任务队列)。

每个 tick 必须处理完所有的 微任务队列中的事件。

5. 语句顺序

JS 编译器在编译 JS 代码的时候会做优化，对语句重新排序。有些是安全排序，有些是危险排序。

# 回调

1. 嵌套回调与链式回调

在喜欢一系列异步任务按顺序处理的时候，就会出现回调地狱。

回调导致业务处理逻辑复杂

回调是控制反转的，我们无法确定回调函数会返回什么样的数据，所以需要对回调函数的参数做校验。

回调去表达程序异步和管理并发的两个主要缺陷：缺乏顺序性和可信任性。

# Promise

Promise 解决的问题：

```
1. 地狱式回调
2. 回调执行次数过多或过少。then 只会被执行一次
3. 吞掉错误或异常。catch 会处理异常
4. 逻辑流，链式调用
```

2. Promise 模式

`Promise.all([..])`: 同时执行多个 Promise 任务，并同时获得两个结果。

`Promise.race([...])`：同时执行多个 Promise 任务，取第一个结果。借助这个功能可以实现一个超时竞赛的功能):

```js
// 为foo()设定超时 
Promise.race( [
foo(), // 启动foo()
timeoutPromise( 3000 ) // 给它3秒钟 
])
.then(
    function(){
        // foo(..)按时完成! 
    },
   function(err){
        // 要么foo()被拒绝，要么只是没能够按时完成， // 因此要查看err了解具体原因
    } );
```

3. Promise 局限性

单一值：对于需要返回多个值的情况不适合

无法取消

性能：Promise 相比较 回调 来说要执行的步骤更多

# 生成器

> 不看了，不用上


# 第5章 程序性能

1. web worker

JS 实现多线程执行。

Web Worker 与主程序直接不会共享任何作用域或资源，没有多线程需要处理的并发问题。

Web Worker 与主程序通过事件监听方式做交互。

# 第6章 性能测试与调优

1.  性能测试

如何测试某个运算的速度？

```js
方式1：运行前后记录时间，得到时间差。

局限性：对于ms以下量级的运算不适合；只能计算一次的时间，不能代表大多数情况；

重复执行取平均值：
```

>后续没看，用不上


