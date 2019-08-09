---
title: JavaScript 权威指南
date: 2019-06-25 13:18:12
tags: [JavaScript]
category: 读书笔记

---

# 前言

2019/7/5 完成第一遍阅读

# 第一章 JavaScript 概述

JavaScript 语言标准: ECMAScript 标准 (对应 JavaScript 1.3)

1. 所有游览器都实现了 ES3

# 第二章 词法结构

1. JavaScript 使用 Unicode 字符集编写

# 第三章 类型，值和变量

1. 数据类型：能够表示并操作的值的类型
2. JavaScript 中数据类型：原始类型(primitive type), 对象类型(object type)
3. 对象类型(普通对象，特殊对象(array, function, Date. RexExp, Error))
4. JavaScript 解释器由自己的内存管理机制，可以自动回收垃圾。当对象没引用的时候会被回收。
5. JavaScript 的类型也可以分为：拥有方法的类型 和 不能拥有方法的类型(null undefined)
6. JS 可以自由进行数据类型切换。(比如在 if 语句中使用了 number 类型的数据会自动转换成 bool)
7. JS 中算术运算, 数值溢出/下溢 使用 无穷大infinity/负无穷大-infinity 表示
8. JS 算术运算得到一个非数字时: NaN (比如 0/0)

JS 中非数字值和任何值都不相等
判断值是不是非数字值: x != x 返回 true 表示 x 是 NaN

9. JS 使用二进制浮点数，只能表示其中有限的个数，所以 JS 可能会使用一个近似值表示需要表示的值

所以在金融计算使用大整数进行，比如使用 分作为单位
在比较两个值是否相等的时候要格外注意这个问题

10. JS String, 不可变值，ES5 后 String 可以当作只读数组(可以通过 str[0] 反问里面的值)

11. JS 全局对象
12. 包装对象，基本数据包装成对象, String Number Boolean, 建立临时对象并且调用这个临时对象的函数(函数调用完成就会销毁)

```
var s = 'test';
s.a = 4;    // 这个修改只是发生在临时对象上
var sa = s.a; // undefined, s 对象已经销毁了
```

13. 所有对象继承了两个转换方法：`toString()`, `valueOf()`

14. 动态语言：在运行期间才会做数据类型检查的语言。
15. 如果直接给一个没有声明过的变量赋值，这个变量会变成全局变量

```
b = 2; // 合法，但是不推荐这么使用，所以在使用变量前一定要用 var 声明
```

16. JS 中没有块级作用域，有函数作用域(比如 if 块内定义的变量，在函数内可用)；声明提前: 函数内的变量被自动提前声明

# 第四章 表达式和运算符

1. 一元运算符(--)，二元运算符(*)，三元运算符(?:)
2. 其他几个运算符：in, instanceof, eval() 执行js代码字符串，typeof, 

delete 删除对象属性，或者数组元素
void

3. 原型链是 JS 的继承机制。(o instanceof f; 判断 f.prototype 是否在 o 的原型链中存在)
4. && 并不一定返回 true/false, &&返回的是 真值/假值

# 第五章 语句

1. catch 中执行了 return 语句，finally 中的语句还是会执行，如果 finally 中有 return 则会覆盖 catch 中的 return.
2. ES5 中增加了指令 `use strict`, 表示后续代码解析为严格代码

严格代码特点

- 禁止使用 with 语句
- 所有变量必须声明
- 调用的函数中的一个 this 值是undefined(非严格下 this 是全局对象)
- 通过 call, apply 调用的函数，this 就是 call,apply 传入的参数

...

# 第六章 对象


1. 对象是 JavaScript 的基本数据类型。
2. 对象创建方式有三种

- 对象直接量

```
var user= { name: 'fang', age: 12};
```

- new

通过原型继承创建一个新对象

```js
function inhreit(p){
    if(p == null) throw TypeError();
    if(Object.create){
        return Object.create(p);
    }
    var t = typeof p;
    if(t !== 'object' && t !== 'function') throw TypeError();
    function f() {};
    f.prototype = p; // 让 f 继承 p
    return new f();
}
```

- Object.create(), ES5 新增

创建一个空对象: `var 03 = Object.create(Object.prototype)`

3. 原型

通过 new Date() 创建的对象，原型是 `Date.prototype`

4. 属性查询和设置

- `., user.name`
- `[], user['name']`

5. 属性访问错误

因为 null undefined 值都没有属性，所以

```js
book.sub // 不会报错，返回 undefined,
book.sub.length // 报错 book 对象没有 sub 属性，
```

6. 可以使用 `for...in` 语句遍历对象的所有属性

```js
var object1 = {a: 1, b: 2};
for(var val in object1){
    //...
}
```

7. 对象的三个属性

- 原型 prototype
- 类 class

是一个字符串，Object 的类属性: `[object class]`

- 可扩展性 extensible attribute

表示是否可以给对象添加新属性。

判断对象是否可扩展：`Object.esExtensiblie(xx)`

8. 对象的方法，JavaScript对象都从 `Object.prototype` 继承属性(除了通过显型式创建的对象)

- toString()
- toLocalString(): 返回对象的本地化字符串, 默认和 toString() 一样
- valueOf(): 数值转换的时候使用

# 第七章 数组

1. JS 数组中的元素可以是不同类型。
2. JS 数组是动态的，创建时无需指定大小。
3. 继承自 Array.prototype 中的属性
4. 数组遍历

- for i
- for in
- forEach

5. Array.prototype 中提供的一些操作数组的函数

- join(); 将数组中的元素转化成字符串拼接
- reserve(); 数组逆序
- sort(); 排序
- concat(); 添加数组元素
- slice(); 获取子串
- push();
- pop();
- unshift();
- shift();

ES5:

- forEach
- map
- filter
- every(), sime()
- reduce(), reduceRight()
- indexOf(), lastIndexOf();

# 第八章 函数

1. JS 函数可以嵌套在其他函数中定义。
2. 嵌套函数，有趣之处在于，内部函数可以访问外部函数的变量
3. 函数调用 4 种方式

- 作为函数
- 作为方法
- 作为构造函数
- 通过它们的 call(), apply() 方法间接调用

4. `a = a || []` 如果 a 为真值就返回 a, 假值就赋值为 `[]`
5. `arguments` 指向实参对象啊的引用，实参对象是一个数组，ES5 严格模式下 `arguments` 只读
6. 作为命名空间的函数. 函数内的变量外部是不可见的

```
(fucntion(){
    // ...
}()); // 结束函数定义并立即调用它
```

7. 闭包：函数定义时的作用域链到函数执行时依然有效。
8. 可调用对象，不是所有可以调用的都是函数。比如 RegExp, getElementsById();
9. 高阶函数：操作函数的函数。


# 第九章 类和模块

1. new 关键字来调用构造函数，构造函数的 prootype 属性被用做新对象的原型

```
function Range(from, to){
    this.from = from;
    this.to = to;
}

Range.prototype = {
    includes: function(x) { return this.from <= x && x <= this.to;},
    foreach: function(f) {
        //....
    },
    toString: function(){
        return ".....";
    }
}

var r = range(1, 3); //创建对象
```

2. 原型对象是类的唯一标识

```
r instanceof Range // 如果 r 继承自 Range.prototype, 则返回 true, 支持间接继承
```

3. 任何 JS 函数都可以作为构造函数，并且调用构造函数需要一个 prototype 属性

```
var F = function(){};
var o = new F();
o.constructor === F // true
```

显示添加构造函数

```js
function Range(from, to){
    this.from = from;
    this.to = to;
}
Range.prototype = {
    constructor: Range,
    includes: function(x) { return this.from <= x && x <= this.to;},
    foreach: function(f) {
        //....
    },
    toString: function(){
        return ".....";
    }
}
```

4. 模块

CommonJS 服务器端 JavaScript 标准规范。

5. 用作命名空间的对象，避免污染全局变量。

# 第10章 正则表达式的模式匹配

RegExp

1. 正则表达式定义方式

```js
var pattern = /s$/; // 匹配所有 s 结尾的字符串， 直接量
var pattern2 = new RegExp("s$"); // 对象的创建
```

# 第11章 JavaScript 的子集和扩展


# 第12章 服务器端 JavaScript


1. Node 是基于 C++ 的高速 JavaScript 解释器，绑定了用于进程，文件和网络套间字等底层 Unix API. 还绑定了 HTTP 客户端和服务器 API.
2. Node 在 Google V8 JavaScript 引擎上构建而成.


# 第 13 章 Web 游览器中的 JavaScript

1. Window对象是所有客户端 JavaScript 特性和 API 的主要接入点
2. window 重要属性 Location, document
3. window.onload，表示当窗口中的文档内容稳定后调用。
4. web游览器提供的高级服务(网络，图像，数据存储, 网络: XMLHttpRequest 对象)
5. HTML 文档嵌入客户端 JS 代码 4 种方式

- 内联 `<script></script>`
- 外部文件 `<script> src 属性指定`
- HTML 事件处理程序, `onclick/onmouseover` 等 html 属性值指定
- URL, `javascript:` 协议

6. 脚本类型指定 `<script>` type 属性默认就是 `text/javascript`; 

老的游览器使用 `language 属性代替 type` 属性(已经弃用)

7. JavaScript程序：当前页面中所有的 JS 代码，`iframe` 中的 JS 作为独立的 JS 程序
8. 客户端 JS 线程模型

JS 客户端是单线程执行。编写代码时可以保证两个事件处理程序不会同一时刻运行。不用担心锁，死锁。

9. 客户端 JS 时间线

    1. Web 游览器创建 Document 对象，开始解析 HTML 内容。(创建 Element 对象和 Text 节点到文档), 文档解析过程中 document.readyState 属性变成 `loading`
    2. HTML 解析器，解析过程中遇到没有 async, defer 属性的 `script` 元素时，下载脚本并且执行脚本，过程中解析器停止。
    3. 解析器遇到 async 属性的 script 时，脚本下载过程中解析器不停止，脚本会在它下载完成后尽快执行。
    4. 文档解析完成后 document.readyState 属性变成 `interative`

10. JS 不能做的事

防止恶意代码

- 第一道防线：JS 不支持某些功能。(比如：没有权限写入或删除计算机上的文件)
- 第二道防线：在自己支持的某些功能上施加限制。(比如：部分游览器不支持通过 JS 打开新窗口；JS 可以关闭窗口但是必须用户确认；HTML FileUpload value 属性对于 js 只读；同源策略，脚本不能在来自不同服务器的文档上注册事件监听器)

11. 同源策略：是对 JS 代码能够操作哪些 Web 内容的一条完整的安全限制。

脚本只能读取和所属文档来源相同的窗口和文档的属性。

防止恶意脚本读取其他窗口上的信息。

12. 同源策略有3种不严格的模式

不严格的同源策略技术：跨域资源共享 `新的 Origin, Access-Control-Allow-Origin`, 允许服务器显式列出源。(可以使用这种方式实现跨域网络请求)

另一种: 跨文档消息(cross-document messaging)

13. 跨站脚本 XSS, 

例子：把用户输入的内容不做处理，直接显示在页面上，就会被攻击（用户输入内容可能保护 恶意JS 代码）

14. 拒绝服务攻击(游览器无限弹出 alert() 对话框)

# 第 14 章 Window 对象

1. 计时器, 全局函数，windows 对象的方法

`setTimeout(), setInterval()`

2. 游览器定位和导航

window.location 对象引用的是 Location 对象。

document.location 对象也是 Location 对象。

3. 解析 URL

Location 对象 href 属性是字符串，包含完整 URL.

Location: url 相关属性

```
protocol
host
hostname
port
pathname
search
hash
```

加载新文档: `assign(), replace() 不会有历史栈, reload()`

4. 游览历史 history 对象 `window.history`
5. `window.navigator`

```
appName: 游览器全称
appVersion: 游览器版本号
userAgent
platform: 操作系统
```

6. `window.screen`
7. `window.alert(), window.confirm(), window.prompt()`

# 第 15 章 脚本化文档

1. 文档对象模型, Document Object Model, DOM , 代表和操作文档的内容
2. 选取文档元素的几种方法

- id: `document.getElementById("id")`
- name: `document.getElementsByName('name')`
- 标签名字: `document.getElementsByTagName('name')`
- css 类: `document.getElementByClassName('name')`, 标准模式下区分大小写
- css 选择器: 

3. document 中的节点对象 Node

```
parentNode
childNodes
firstChild
lastChild
nextSibling, previoursSibling
nodeType, nodeVale, nodeName
```

4. Element 对象由一个标签和一组属性组成.
5. 查询元素的几何尺寸: `getBoundingClientRect()`
6. 滚动 `scrollTop`
7. document 属性

```
cookie
domain
lastModified
location
referrer
title
URL
```

# 第 16 章 脚本化 CSS

1. 层叠样式表(Cascading Style Sheet, CSS) 是一种指定 HTML 文档视觉表现的标准

# 第 17 章 事件处理

1. 客户端 JS 程序采用了异步事件驱动编程模型。
2. 传统事件类型

```
- form: submit, reset
- focus, blur
- 鼠标事件: mousemove, mousedown, mouseup
- 键盘事件: keyup, keypress, keyup
```

3. 事件注册：`addEventListener(), removeEventListener()`

IE9 之前适配: `attachEvent, detachEvent`


4. 事件取消

```
function cancelHandler(event){
    var event = event || window.event; // IE

    if(event.preventDefault) event.preventDefault();
    if(event.returnValue) event.returnValue = false; // IE
    return false; // 用于处理使用对象属性注册的处理程序
}
```

# 第 18 章 脚本化 HTTP

1. XMLHttpRequest 类上定义了 HTTP API. 每个实例都表示一个独立的请求

```
var request = new XMLHttpRequest();
```

3. 跨域请求: 通过发送合适的 CORS(Cross-Origin Resource Sharing) 实现跨域
4. JSONP: 借助 `<script> 里面的 src 属性` 发送 HTTP 请求

# 第 19 章 jQuery 类库

# 第 20 章 客户端存储

1. localStorage/sessionStorage

区别：存储的有效期和作用域不一样

localStorage: 数据是永久的，作用域限定在同源文档中(文档源: 协议+主机名+端口)

sessionStorage: 数据有效期和标签页生命周期一样，作用域：同源文档，窗口(不同标签页之间无法共享)

2. cookie

有效期：默认有效期和回话生命周期一样；可以设置有效期 `max-age`;

作用域：和游览器进程一样，所有标签页可以共享，限定同源文档。

```
document.cookie = "version=" + encodeURIComponent(document.lastModified);

name=value;max-age=seconds
```

cookie 有大小显示 4k。

3. 数据库
4. 文件系统

# 第 21 章 多媒体和图形编程

# 第 22 章 HTML5 API



1. WebSocket, 实现双向通信

# 客户端 JavaScript 参考

各种 API








