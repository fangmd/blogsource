---
title: 廖雪峰 js
date: 2016-08-07 13:18:12
tags: [js]
category: 读书笔记

---

# JavaScript 简介

## 历史
网景公司员工 Brendan Eich 10天完成了javaScript的初期设计。

它与java毫无关系。

## ECMAScript
是JavaScript语言的标准

## JavaScript 版本

2015年6月 ECMAScript 6（ES6）

<!--more-->

# 快速入门

**js代码存放位置**

1. js代码可以直接嵌在网页任何地方，不过通常把它放在`<head>`中。`<script>...</script>`包含js代码

2. 将js代码存放在单独的`.js`文件中，在HTML中通过`<script src=".."></script>`引入文件。
	这样做有利于维护


## 基本语法

JS语法和java语法类似，语句以`；`结尾（非必须），语句快`{。。。}`

## 数据类型
- number
- string
- boolean
- &&
- ||
- !
- 比较运算符
- null
- unfefined
- 数组
- 对象
- 变量


### strict 模式
如果一个变量没有声明就使用，这个变量就会变成全局变量。如果在同一页面有多个js文件，其中使用了变量名相同的全局变量就会造成错误。

strict模式：强制js代码中必须声明变量

开启strict：

	`use strict`;

## 字符串
字符串可以使用`\`\``,`""`

转义字符： `\\`

ASCLL字符可以使用`\x##`形式的十六进制表示。

Unicode字符可以使用`\u####`表示

### 多行字符

	`。。。。`

低版本需要使用`\n`来表示换行

### 模板字符串

使用`+`号将多个字符串连接起来。

	var name = '小明';
	var age = 20;
	var message = '你好, ' + name + ', 你今年' + age + '岁了!';
	alert(message);

使用`$`:

	var name = '小明';
	var age = 20;
	var message = `你好, ${name}, 你今年${age}岁了!`;
	alert(message);

### 操作字符串

	var s = "hello world"
	s.length; // 长度
	s[0];	// 索引操作

**注意字符串是不可变的：**

	var x = 'Test';
	s[0] = 'X';
	// s 还是 Test

JavaScript为字符串提供了一些常用方法，注意，调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串：

	s.toUpperCase();
	var lower = s.toLowerCase();
	var index = s.indexOf("world");
	var sub = s.substring(5,9);// 包头不包尾
	s.substring(5); // 5到末尾

## 数组

js中的`Array`可以存储任意数据类型

	var arr = [1, 2, 3.14, 'Hello', null, true];

长度：

	arr.length;

**注意：**对数组的长度赋值，会改变数组的长度

	arr.length = 2;

通过索引修改里面的值：

	arr[1] = 99;

如果赋值的索引超过了数组的长度，也会改变数组长度,中间的位置由`undefined`填充

大多数的编程语言都不允许数据越界操作，在js虽然支持这个操作但是不建议这样做。

搜索指定元素的位置：

	arr.indexOf("a");

截取片段：包头不包尾

	arr.slice(0,2);
	arr.slice(3);

复制数组：

	var aCopy = arr.slice();

操作最后一个元素：

	arr.push('1','2'); // 返回Array新的长度
	arr.pop();// 返回被删除的最后一个元素，空数组也不会报错返回undefined

操作数组头部：

	arr.unshift('1','1');// 返回Array新长度
	arr.shift(); // 返回被删除的头元素，同样，空数组也不会报错，返回undefined

排序：

	arr.sort();

反转：

	arr.reverse();

从指定位置删除或者添加元素：

	arr.splice(2,3,`google`,`facebook`);// 返回删除的元素，表示删除从2号位开始的3个元素，从2号位开始插入google，facebook

连接数组：该方法没有修改源数组，会生成新的数组

	var arr = ..
	var added = arr.concat([1,2,3]);
	var arr = ['A', 'B', 'C'];
	arr.concat(1, 2, [3, 4]); // ['A', 'B', 'C', 1, 2, 3, 4]// 可以自动拆分Array

join：

	var arr = ['A', 'B', 'C', 1, 2, 3];
	arr.join('-'); // 'A-B-C-1-2-3'

多维数组：

	var arr = [[1, 2, 3], [400, 500, 600], '-'];

## 对象

js中对象是一种无序的集合数据类型，由若干个见值对组成。

用`{..}`表示对象，键值对：`XXX:XXX`形式，如果属性中包含特殊字符需要用`""`包含，其访问方式也不一样：

	xiaohong['middle-school']; // 'No.1 Middle School'
	xiaohong['name']; // '小红'
	xiaohong.name; // '小红'

访问不存在的属性也不会报错：返回undefined

对象操作比较自由：

	var xiaoming = {
	    name: '小明'

	};
	xiaoming.age; // undefined
	xiaoming.age = 18; // 新增一个age属性
	xiaoming.age; // 18
	delete xiaoming.age; // 删除age属性
	xiaoming.age; // undefined
	delete xiaoming['name']; // 删除name属性
	xiaoming.name; // undefined
	delete xiaoming.school; // 删除一个不存在的school属性也不会报错

检查对象是否有某个属性：

	'name' in xiaoming; // ture，返回boolean

继承的属性也会返回true：

	'toString' in xiaoming; // true

判断属性是否是自己的：

	var xiaoming = {
	    name: '小明'
	};
	xiaoming.hasOwnProperty('name'); // true
	xiaoming.hasOwnProperty('toString'); // false

## 条件判断

1. `if(){...} else {...}`
	else 为可选项，如果语句快中只有一句，可以省略花括号。建议不要这样做。
2. 多行判断`else if`
3. 条件语句中如果不是`true`在js中就视为`false`

## 循环

### for

1. for
2. break退出循环

循环一个变量的属性值： for in

	for(var key in o){ // o 是一个对象
		alert(key);
	}

过滤属性：

	for (var key in o) {
		if (o.hasOwnProperty(key)) {
		        alert(key); // 'name', 'age', 'city'

		}

	}

Array也是对象，`for in `可以直接使用：

	for(var i in arr){
		alert(i); // 打印 索引 0 1
	}

注意：这里i得到的是string而不是number

### while

### do while

## Map Set
ES6新增数据类型，游览器可能不支持。

### Map
键值对

创建与相关操作：

	var map = new Map([['Micheal',95], ['Bob',75], ['Tracy',85]]);
	map.get('Michael'); // 95

	var m = new Map();
	m.set('Adam',98);
	m.set('Bob',98);
	m.has('Adam');// return true
	m.get('Adam');// return 98
	m.delete('Adam'); // delete key 'Adam'
	m.get('Adam');// return undefined

一个key只能对应一个值，多次赋值会覆盖前面的值

### Set

创建：

	var s1 = new Set();
	var s2 = new Set([1,2,3]);

重复的元素会被自动过滤。

	s1.add(2);
	s1.delete(2);

## iterable
遍历Array使用索引循环，遍历Map，Set

	for(var x of a){}

`for in`实际上是遍历Array的属性：

	var a = ['A', 'B'];
	a.name = 'Hello';
	for(var x in a){
		alert(x); // '0' '1' ,'name'
	}

`for of`:

	for(var x of a){
		alert(x); // 'A' , 'B'
	}

	var a = ['A', 'B', 'C'];
	a.forEach(function (element, index, array) {
		// element: 指向当前元素的值
	        // index: 指向当前索引
		// array: 指向Array对象本身
		alert(element);

	});


# 函数

## 函数的定义和调用

### 函数定义

定义方式一：

	function abs(x){
		if(x >= 0){
			return x;
		} else {
			return -x;
		}
	}

函数定义，函数名，参数，函数体

如果一个函数没有return，也会返回结果：`undefined`

函数也是一个对象。

定义方式二：

	var abs = function (x){....}; // 匿名函数

### 函数调用:

	abs(10); // return 10
	abs(10, 'bsdfsd'); // return 10 ,无关参数不会影响结果
	abs(); // return NaN 参数为null 计算结果 NaN

参数检查：

	function abs(x) {
		if (typeof x !== 'number') {
		        throw 'Not a number';
		}
		if (x >= 0) {
		        return x;

		} else {
		        return -x;

		}

	}

### arguments
js中的关键字，只在函数内部起作用，指向函数的调用者传入的所有参数。


	function abs() {
	if (arguments.length === 0) {
		return 0;

	}
	    var x = arguments[0];
	        return x >= 0 ? x : -x;

	}


可以判断传入参数的个数。

### rest
参数，写在最后，前面用`...`，多余的参数存入`rest`

js引擎会在末行自动添加分号


## 变量作用域

用`var`声明的变量是有作用域的。

如果一个变量在函数体声明，其作用域是函数体。

js中函数可以嵌套，内部函数可以访问外部函数定义的变量。

如果内部函数和外部函数的变量名重了，后续在使用的时候会从‘内’向‘外’找。

### 变量提升
js函数定义有个特点，它会先扫描整个函数体的语句，把所有申明的变量‘提升’到函数顶部。

在`use strict`下，也可以先使用后声明。

建议：在函数内部先声明所有变量。

### 全局作用域
js中有个默认的全局对象`window`，全局变量会被绑定到其属性中

	'use strict';

	var course = 'Learn JavaScript';
	alert(course); // 'Learn JavaScript'
	alert(window.course); // 'Learn JavaScript'

函数也一样是绑定到`window`的属性中了。

	window.alert();

>这说明JavaScript实际上只有一个全局作用域。任何变量（函数也视为变量），如果没有在当前函数作用域中找到，就会继续往上查找，最后如果在全局作用域中也没有找到，则报ReferenceError错误。

### 名字空间

不同的js文件如果使用了相同的全局变量，或者相同名字的顶层函数，就会造成命名冲突。

**解决方法：**将自己的全局变量都绑定到一个全局变量中：

	// 唯一的全局变量 MYAPP
	var MYAPP = {}

	// 其他变量
	MYAPP.name = 'myapp';
	MYAPP.varsion = 1.0;

	// 其他函数
	MYAPP.foo = function(){...};

把自己的代码放在唯一名字空间`MYAPP`中。

### 局部作用域
js中变量作用域是函数内部，所以`for`循环这种语句块是无法定义局部变量的，他们定义的变量函数内部还是可以访问到。

**块级作用域：**`let`

### 常量

js无法定义常量，只能用变量名规范设置常量：

	var PI = 3.14;

大写的变量名，规定表示常量，不要去修改该它。

ES6：引入常量：

	const PI = 3.14;
	PI = 3;// 不会报错，但是无效

## 方法

在一个对象中绑定函数，称为这个对象的方法。

	var xiaoming = {
		name:'xiaoming'
		birth:1990;
		age:function(){
			var y = new Date().getFullYear();
			return y - this.birth;
		}
	};

this:表示指向当前变量。

如何在函数内部的函数调用当前对象：

	// 在第一层函数
	var that = this;
	// 后续使用 that 就可以获取属性

### apply
修复函数的this指向：

	function getAge() {
      		var y = new Date().getFullYear();
        	return y - this.birth;

	}

	var xiaoming = {
		name: '小明',
        	birth: 1990,
		age: getAge

	};

	xiaoming.age(); // 25
	getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空

`call()`与`apply()`区别：

- `apply()`把参数打包成`Array`传入
- `call()`把参数按顺序传入


	Math.max.apply(null, [3, 5, 4]); // 5
	Math.max.call(null, 3, 5, 4); // 5

对于普通函数，通常把`this`绑定为`null`

### 装饰器

利用`apply()`,动态改变函数的行为。

js的所有对象都是动态的，即使是内置的函数，可以重新指向新的函数。

计算所有的函数调用次数：


	var count = 0;
	var oldParseInt = parseInt; // 保存原函数

	window.parseInt = function () {
		count += 1;
	        return oldParseInt.apply(null, arguments); // 调用原函数

	};

	// 测试:
	parseInt('10');
	parseInt('20');
	parseInt('30');
	count; // 3



## 高阶函数
Higher-order function

参数接收函数。

### map
`map()`方法定义在`Array`中

一个作用：

	functionpow(x) {
		return x * x;

	}

	var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
	arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]

把数字转化成字符串：

	arr.map(String);

### reduce

	[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)

### filter

过滤掉Array的一些元素。

	var arr = [1,2,3,4,5];
	var r = arr.fliter(

		function(x){
			return x % 2 != 0; // 返回true表示留下，false表示过滤
		}

	);

把一个`Array`中的空字符串删除掉：

	var arr = ['A', 'B', null, undefined, 'C', '']
	var r = arr.fliter(function(s){
		return s && s.trim(); // 注意：IE9以下的版本没有trim()方法
	});

### sort

	var arr = [10, 20, 1, 2];
	arr.sort(function (x, y) {
	if (x < y) {
	        return -1;

	}
	if (x > y) {
	        return 1;

	}
	    return 0;

	}); // [1, 2, 10, 20]

默认情况下，对字符串排序，是按照ASCII的大小比较的，现在，我们提出排序应该忽略大小写，按照字母序排序。要实现这个算法，不必对现有代码大加改动，只要我们能定义出忽略大小写的比较算法就可以：

	var arr = ['Google', 'apple', 'Microsoft'];
	arr.sort(function (s1, s2) {
	    x1 = s1.toUpperCase();
            x2 = s2.toUpperCase();
	if (x1 < x2) {
	        return -1;

	}
	if (x1 > x2) {
	        return 1;

	}
	    return 0;

	}); // ['apple', 'Google', 'Microsoft']

`sort`会对`Array`进行修改，返回的也是当前`Array`

## 闭包

### 函数作为返回值

	function lazy_sum(arr){
		var sum = function(){
			return arr.reduce(function(x,y){
				return x+y;
			});
		};
		return sum;
	}

当我们调用`lazy_sum()`时不会返回结果，而是求和函数：

	var f = lay_sum([1,2,3,4]);// function sum();

调用f：

	f(); // 返回 10;

### 闭包

返回一个函数，延迟执行，注意里面不要有`for，i`

比如：

	function count() {
	    var arr = [];
	    for (var i=1; i<=3; i++) {
	    arr.push(function () {
                return i * i;

	    });

	    }
	        return arr;

	}

	var results = count();
	var f1 = results[0];
	var f2 = results[1];
	var f3 = results[2];

	f1(); // 16
	f2(); // 16
	f3(); // 16

原因：在于返回的函数引用了变量`i`，但是它不是立即执行，等到第三个函数都返回的时候，`i`变成了`4`

闭包不要引用任何循环变量。

如果一定要使用循环变量：

	function count() {
	    var arr = [];
	    for (var i=1; i<=3; i++) {
		    arr.push((function (n) {
			    return function () {
                	    return n * n;                
			    }

		    })(i));

    	    }
            return arr;

	}

匿名函数创建与调用：

	(function(x){return x*x})(3);

借助闭包封装私有变量：

	function create_counter(initial){
		var x = initial || 0;
		return {
			inc:function(){
				x += 1;
				return x;
			}
		}
	}

使用：

	var c2 = create_counter(10);
	c2.inc(); // 11
	c2.inc(); // 12
	c2.inc(); // 13

	var c1 = create_counter();
	c1.inc(); // 1
	c1.inc(); // 2
	c1.inc(); // 3

>在返回的对象中，实现了一个闭包，该闭包携带了局部变量x，并且，从外部代码根本无法访问到变量x。换句话说，闭包就是携带状态的函数，并且它的状态可以完全对外隐藏起来。

把多参数的函数变成单参数：

	function make_pow(n){
		return function (x){
			return Math.pow(x ,n);
		}
	}

	var pow2 = make_pow(2);
	var pow3 = make_pow(3);

	pow2(5);//25
	pow3(7);// 343


## 箭头函数
ES6新增函数：Arrow Function

	x=>x*x;

	funtion(x){ return x*x; }

相当于是匿名函数，简化了函数的定义，有两种格式：

	x=>{
		if(x>0){
			return x*x;
		}else{
			return -x*x;
		}
	}

如果有多个参数：

	(x,y)=>x*x*y;

	()=>3.24; // 无参
	(x,y, ...rest)=>

如果要返回对象：

	x=>({foo:x})

### this
在箭头函数中this，是词法作用域由上下文确定。

## generator
生成器，ES6加入的数据类型,看上去像函数，但可以返回多次。

	function* foo(x){
		yield x+1;
		yield x+2;
		return x+3;
	}

调用：

	var f = foo(4);
	f.next();
	f.next(); // {value: , done:false}
	f.next(); // {value: 7, done:true}

	for(var x of foo(4)){
		console.log(x); //
	}

# 标准对象

`typeof`

	typeof 123; // 'number'
	typeof NaN; // 'number'
	typeof 'str'; // 'string'
	typeof true; // 'boolean'
	typeof undefined; // 'undefined'
	typeof Math.abs; // 'function'
	typeof null; // 'object'
	typeof []; // 'object'
	typeof {}; // 'object'


返回的是字符串

**注意：**使用`type`无法区分 `null`，`Array`和通常意义上的object`{}`

### 包装对象

	var n = new Number(123);
	var s = new String('str');
	var b = new Boolean(true);

	typeof new Number(123); // 'object'
	new Number(123)==123;	// false

建议：不要使用包装对象

`Number()`,`Boolean()`,`String()`可以做为普通函数，做类型转换。

	var n = Number('123');
	typeof n; // 'number' 转换后的类型不是包装对象，可以直接使用

**总结规则:**

1. 不要使用`new Number()`..包装对象
2. 用`parseInt()`,或者`parseFloat()`来转换任意类型到`number`
3. 用`String()`来转换任意类型到`string`，或者直接调用某个对象的`toString()`方法
4. 通常不必把任意类型为`boolean`,可以直接写：`if(myVar){...}`
5. `typeof`操作符可以判断出`number`，`boolean`，`string`，`function`，`undefined`
6. 判断`Array`要使用`Array.isArray(arr)`
7. 判断`null`使用`myVar==null`
8. 判断某个全局变量是否存在`typeof window.myVar == 'undefined'`
9. 函数内部判断某个变量是否存在用`typeof myVar == 'undefined'`

不是任何对象都有`toString()`方法，`null`，`undefined`没有

	123..toString(); // '123' 注意有两个点
	(123).toString(); // '123'

## Date
js中`Date`对象用来表示日期和时间。

	var now = new Date();
	now; // Wed Jun 24 2015 19:49:22 GMT+0800 (CST)
	now.getFullYear(); // 2015, 年份
	now.getMonth(); // 5, 月份，注意月份范围是0~11，5表示六月
	now.getDate(); // 24, 表示24号
	now.getDay(); // 3, 表示星期三
	now.getHours(); // 19, 24小时制
	now.getMinutes(); // 49, 分钟
	now.getSeconds(); // 22, 秒
	now.getMilliseconds(); // 875, 毫秒数
	now.getTime(); // 1435146562875, 以number形式表示的时间戳

**注意：**获取的是本机操作系统的时间，可以被用户更改。、

创建指定时间的方法一：

	var d = new Date(2015,3,12,34,23,23,123);
	d; //

js中`0`表示一月。

创建指定时间方法二：

	var d = Date.parse('2015-06-24T19:49:22.879+08:00');
	d; // 1435146562875

	var d = new Date(1435146562875);
	d; // Wed Jun 24 2015 19:49:22 GMT+0800 (CST)

### 时区

Date对象表示的时间总是按浏览器所在时区显示的，不过我们既可以显示本地时间，也可以显示调整后的UTC时间：

	var d = new Date(1435146562875);
	d.toLocaleString(); // '2015/6/24 下午7:49:22'，本地时间（北京时区+8:00），显示的字符串与操作系统设定的格式有关
	d.toUTCString(); // 'Wed, 24 Jun 2015 11:49:22 GMT'，UTC时间，与本地时间相差8小时


在js中不需要进行时区转换，只要传递的是一个`number`类型的时间戳就不用关心时间戳。游览器可以将时间戳转换成为本地时间。

获取时间戳：

	if (Date.now) {
		alert(Date.now()); // 老版本IE没有now()方法

	} else {
        	alert(new Date().getTime());

	}

## RegExp

正则表达式

- `\d`:数字
- `\w`:字母或数字
- `.`:任意字符
- `*`:表示任意个字符
- `+`:表示至少一个字符
- `？`:表示0个或者1个字符
- `{n}`:表示n个字符
- `{n,m}`:表示n-m个字符
- `[]`:表示范围，一个字符
- `^`:行开始
- `$`:行结束


### RegExp

	var re1 = /ABC\-001/;
	var re2 = new RegExp('ABC\\-001');

第二种写法需要转意符

使用:`text()`

	re1.text('nnnnnnnn'); // false

### 切分字符串

	'a b  c'.split(' '); // ['a','b',' ',' ','c']
	'a b  c'.split(/\s+/); // ['a','b','c']
	'a,b, c  d'.split(/[\s\,]+/); // ['a', 'b', 'c', 'd']
	'a,b;; c  d'.split(/[\s\,\;]+/); // ['a', 'b', 'c', 'd']

也可用于将不规范的输入转化成正确的数组。

### 分组
正则表达式提取子串功能

	var re = /^(\d{3})-(\d{3,8})$/;
	re.exec('010-12345'); // ['010-12345', '010', '12345']
	re.exec('010 12345'); // null

使用`exect()`方法提取正则表达式中定义的组

	var re = /^(0[0-9]|1[0-9]|2[0-3]|[0-9])\:(0[0-9]|1[0-9]|2[0-9]|3[0-9]|4[0-9]|5[0-9]|[0-9])\:(0[0-9]|1[0-9]|2[0-9]|3[0-9]|4[0-9]|5[0-9]|[0-9])$/;
	re.exec('19:05:30'); // ['19:05:30', '19', '05', '30']

### 贪婪匹配

匹配尽可能多的字符

	var re = /^(\d+)(0*)$/;
	re.exec('102300'); // ['102300', '102300', '']

	var re = /^(\d+?)(0*)$/;
	re.exec('102300'); // ['102300', '1023', '00']

### 全局搜索

	var r1 = /test/g;
	// 等价于:
	var r2 = new RegExp('test', 'g');

	var s = 'JavaScript, VBScript, JScript and ECMAScript';
	var re=/[a-zA-Z]+Script/g;

	// 使用全局匹配:
	re.exec(s); // ['JavaScript']
	re.lastIndex; // 10

	re.exec(s); // ['VBScript']
	re.lastIndex; // 20

	re.exec(s); // ['JScript']
	re.lastIndex; // 29

	re.exec(s); // ['ECMAScript']
	re.lastIndex; // 44

	re.exec(s); // null，直到结束仍没有匹配到
