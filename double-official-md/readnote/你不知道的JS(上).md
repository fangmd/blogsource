---
title: 你不知道的JS(上) 笔记
date: 2020-01-04 11:20:16
tags: [JavaScript]
categories: 读书笔记

---

**作用域和闭包**

# 作用域是什么

## 编译原理

JS 代码在执行之前要编译。

编译过程：通过语法分析将代码转化成 AST语法树，把 AST语法树转化成机器语言。

##  理解作用域

引擎在处理变量的时候，要查找作用域，查找作用域分为：LHS, RHS

LHS 查找如果变量不存在，就会自动创建并赋值给全局作用域中。

RHS 查找变量不存在时报错：ReferenceError

作用域层层嵌套，最后找到全局作用域

# 词法作用域

>全局变量会自动成为全局对象的属性，所以可以通过全局对象属性直接访问到全局变量 `window.a`

欺骗作用域：eval(), with. 对性能有影响。

引擎会在编译时期对作用域查找做优化。

# 函数作用域和块作用域

>外部作用域无法反问内部作用域的变量。内部作用域可以访问外部作用域的变量

函数作用域符合设计规范：最小授权，最小暴露原则。

作用域作用：

1. 避免命名冲突
2. 模块管理
3. 符合设计规范（最小暴露原则）

自执行函数: IIFE

```js
(function foo(){
 // ...
})()

// 写法 2
(function(){
// ...
}())

// 传递参数
(function foo(global){
 // ...
})(window)
```

块作用域：`for`, `if..else`， `try/catch`

>ES6之前使用 var 声明并不会达到块作用域的效果。这里有个变量提升的问题。使用 let 修饰可以避免这个问题
>变量声明应该距离使用的地方越近越好。

1. let 关键字可以将变量绑定在任意作用域中(块级作用域)
2. 块作用域可以优化垃圾收集，块作用域内部变量执行后可以直接被垃圾回收。

# 提升

变量和函数的声明会在任何代码被执行之前首先被处理。

原因是：引擎在执行 JS 代码之前，要先编译代码，编译的一部分工作就是找到所有的声明。

声明会被提升，赋值不会提升。函数首先会被提升，然后是变量。

```
foo();
function foo() {
console.log( a ); // undefined var a = 2;
}

foo(); // 不是 ReferenceError, 而是 TypeError!
var foo = function bar() { // ...
};

foo(); // TypeError
bar(); // ReferenceError
var foo = function bar() { // ...
};

// 声明会被覆盖
foo(); // 3
function foo() { console.log( 1 );
}
var foo = function() { console.log( 2 );
};
function foo() { console.log( 3 );
}
```

# 作用域闭包

闭包：当函数内部使用了函数外部的变量的使用就产生了闭包。而闭包是对外部作用域的引用。

1. 闭包可以阻止外层作用域的垃圾回收，因为闭包函数内部使用了外部的作用域的变量
2. 很多回调函数实际上也是闭包
3. 函数在作用域之外执行，形成闭包

```js
// 输出 6 6 6 6 6
for (var i=1; i<=5; i++) { 
    setTimeout( function timer() {
         console.log( i );
     }, i*1000 );
}

// 输出 12345， 块级作用域+闭包，每个循环都是独立的块作用域，都是独立的 i 变量。
for (let i=1; i<=5; i++) { 
    setTimeout( function timer() {
         console.log( i );
    }, i*1000 );
}
```

## this 与 词法作用域



**this 和 对象原型**

# 关于 this

this 关键字，被自动定义在所有函数作用域中。

this 实现，隐式上下文:

```js
function identify() {
return this.name.toUpperCase();
}
function speak() {
var greeting = "Hello, I'm " + identify.call( this ); console.log( greeting );
}
var me = {
name: "Kyle"
};
var you = {
name: "Reader"
};
identify.call( me ); // KYLE
identify.call( you ); // READER
speak.call( me ); // Hello, 我是 KYLE 
speak.call( you ); // Hello, 我是 READER
```

不使用 this, 显示上下文，把上下文作为参数传入函数:

```js
function identify(context) {
    return context.name.toUpperCase();
}
function speak(context) {
    var greeting = "Hello, I'm " + identify( context ); console.log( greeting );
}
identify( you ); // READER
speak( me ); //hello, 我是 KYLE
```

错误的理解：this 指向函数自己，this 指向作用域。

当一个函数执行的时候，生成一个环境上下文。this 就表示这个上下文。this 在函数被调用的时发生绑定。

# this 全面解析

1. 确定函数调用位置。

函数调用栈，决定了 this 的绑定。

2. 绑定规则

默认绑定: 其他规则不匹配的时候。间接引用的时候。严格模式下 this 不会指向全局对象会指向 undefined。

隐式绑定：函数作为对象的一个属性，函数中 this 自动绑定到对象。

显示绑定：使用 函数的 `.call(..)`, `apply(...)` 时

new 绑定：

3. 绑定优先级

new 绑定 > 显示绑定 > 隐式绑定 > 默认绑定

4. ES6 箭头函数

脱离之前的绑定规则，根据外层作用域来决定 this(使用外部的 this)

# 对象

1. 对象创建的两种形式：声明和构造

```js
// 声明
var obj1 = {
 // ...
}
//构造
var obj = new Object()
```

2. 类型

6 主要类型: `string, number, boolean, null, undefined, object`

内置对象: `String, Number, Boolean, Object, Function, Array, Date, RegExp, Error`

引擎可以自动将 字面量转换成String对象，所以在使用的时候可以作为对象使用。

3. `Object.defineProperty` 可以实现修改对象属性的特性

比如修改 getter, settter， 设置枚举

4. 判断对象是否拥有某个属性

`in` 会查找原型链，`hasOwnProperty` 不会查找原型链

```js
  ("b" in myObject); // false

  myObject.hasOwnProperty( "b" ); // false
```

# 混合对象 “类”

面向对象编程强调的是数据和操作数据的行为本质上是互相关联的，因此好的设计就是把数据和它相关的行为打包（封装）。

# 原型

对象内部有个 `Prototype` 属性，对象在执行 getter/setter 函数的时候先找自身的属性，如果没有找到就从 Prototype 中查找。

1. 使用 `Object.create` 关联 prototype

```js
var myObject = Object.create( anotherObject );
// myObject 是 anotherObject 的子类
```

2. prototype 的尽头是 `Object.prototype`

Object 中有的函数:`.toStrig(), .valueOf(), .hasOwnProperty(), .isPrototypeOf()`

3. 原型链实现继承，实际上是建立了连接

4. 检查类关系

```js
x instanceof Foo; // true 查找原型链判断  x.prototype === Foo.prototype

Foo.prototype.isPrototypeOf(x); // true

// 一种非标准的反问 prototype
x.__proto__ === Foo.prototype; // true
```

```js
Object.defineProperty( Object.prototype, "__proto__", { 
   get: function() {
     return Object.getPrototypeOf( this ); 
  },
  set: function(o) {
      // ES6 中的 setPrototypeOf(..) 
     Object.setPrototypeOf( this, o ); 
     return o;
  } 
} );
```

`Object.create()` 的 `polyfill` 代码, 

```js
if (!Object.create) { 
   Object.create = function(o) {
       function F(){} 
       F.prototype = o; 
       return new F();
   };
}
```

# 行为委托

>[[Prototype]] 机制就是指对象中的一个内部链接引用 另一个对象。



