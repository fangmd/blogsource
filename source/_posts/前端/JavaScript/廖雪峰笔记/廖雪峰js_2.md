---
title: 廖雪峰 js 2
date: 2016-08-07 13:18:12
tags: [js]
category: 读书笔记

---

## JSON

JavaScript Object Notation

json中有的数据类型：

- number
- boolean
- string
- null
- array
- object


<!--more-->

字符集必须是`UTF-8`，字符串和Object的键必须`""`

###　序列化

	var xiaoming = {
		name: '小明',
	        age: 14,
		gender: true,
	        height: 1.65,
		grade: null,
		'middle-school': '\"W3C\" Middle School',
		skills: ['JavaScript', 'Java', 'Python', 'Lisp']

	};

	JSON.stringify(xiaoming); // '{"name":"小明","age":14,"gender":true,"height":1.65,"grade":null,"middle-school":"\"W3C\" Middle School","skills":["JavaScript","Java","Python","Lisp"]}'

	JSON.stringify(xiaoming, null, '  ');　// 按缩进输出

第二个参数用于控制筛选对象的键值，如果只想输出指定的属性，可以传入'Array':
	JSON.stringify(xiaoming,['name','skills'],'');

还可以传入函数处理键值：

	function convert(key, value) {
	if (typeof value === 'string') {
	        return value.toUpperCase();

	}
	    return value;

	}

	JSON.stringify(xiaoming, convert, '  ');

更精确控制序列化：

	var xiaoming = {
		name: '小明',
	        age: 14,
		gender: true,
	        height: 1.65,
		grade: null,
		'middle-school': '\"W3C\" Middle School',
		skills: ['JavaScript', 'Java', 'Python', 'Lisp'],
		toJSON: function () {
			return { // 只输出name和age，并且改变了key：
				'Name': this.name,
				'Age': this.age
			};

		}

	};

	JSON.stringify(xiaoming); // '{"Name":"小明","Age":14}'

### 反序列化
把json转化成对象`JSON.parse()`

	JSON.parse('[1,2,3,true]'); // [1, 2, 3, true]
	JSON.parse('{"name":"小明","age":14}'); // Object {name: '小明', age: 14}
	JSON.parse('true'); // true
	JSON.parse('123.45'); // 123.45

可以接收函数：

	JSON.parse('{"name":"小明","age":14}', function (key, value) {
	    // 把number * 2:
	    if (key === 'name') {
        	    return value + '同学';

	    }
        	return value;

	}); // Object {name: '小明同学', age: 14}

# 面向对象编程

js中的原型链和java的Class区别在于，它没有Class的概念，所有对象都是实例，继承关系不过是把对象的原型指向另一个对象而已。


	var Bird = {
		fly: function () {
		        console.log(this.name + ' is flying...');

		}

	};

	xiaoming.__proto__ = Bird;

	xiaoming.fly(); // 小明 is flying...

**注意：**在js中不要直接使用`obj._proto_`去改变对象的原型，并且低版本不支持。`Object.create()`方法可以传入一个原型对象，并创建新的对象，但是新对象没有属性值，编写一个函数来创建对象：

	// 原型对象:
	var Student = {
		name: 'Robot',
	        height: 1.2,
		run: function () {
	        	console.log(this.name + ' is running...');

		}

	};

	function createStudent(name) {
		// 基于Student原型创建一个新对象:
	        var s = Object.create(Student);
		// 初始化新对象:
	        s.name = name;
		return s;

	}

	var xiaoming = createStudent('小明');
	xiaoming.run(); // 小明 is running...
	xiaoming.__proto__ === Student; // true

## 创建对象

JavaSpcript对每个创建的对象都会设置一个原型，指向它的原型对象。

`obj.XXX`访问一个对象的属性时，js引擎先在当前对象上查找该属性，如果没有找到，就到原型对象上找。一直向上，最后没有找到返回`undefined`

一个数组的原型链：

	arr ----> Array.prototype ----> Object.prototype ----> null

自己创建的一个普通函数：

	foo ----> Function.prototype ----> Object.prototype ----> null
如果一个原型链很长，那么访问一个对象的属性就会花很多时间，建议不要把原型链弄的过长。

### 构造函数

除了直接`{...}`创建对象外，可以使用构造函数：

	function Student(name) {
		this.name = name;
		this.hello = function () {
	            alert('Hello, ' + this.name + '!');

		}

	}

	var xiaoming = new Student('小明');
	xiaoming.name; // '小明'
	xiaoming.hello(); // Hello, 小明!

**注意：**如果不写`new`就是普通函数返回`undefined`

	xiaoming ----> Student.prototype ----> Object.prototype ----> null

一个常用的编程模式：

	function Student(props) {
		this.name = props.name || '匿名'; // 默认值为'匿名'
        	this.grade = props.grade || 1; // 默认值为1

	}

	Student.prototype.hello = function () {
	    alert('Hello, ' + this.name + '!');

	};

	function createStudent(props) {
	    return new Student(props || {})

	}

优点：1. 不需要`new` 2. 参数灵活，可以传可以不传

## 原型继承

JavaScript由于采用原型继承，我们无法直接扩展一个Class，因为根本不存在Class这个类型。

。。。。。。难

## Class 继承

ES6引入新的关键词`class`，目的是让定义类更加简单。

	class Student{
		constructor(name){
			this.name = name;
		}

		hello(){
			alert('Hello,'+this.name+'!');
		}
	}

	var xiaoming = new Student('小明');
	xiaoming.hello();

### 继承

	class PrimaryStudent extends Student{
		constructor(name, grade){
			super(name);
			this.grade = grade;
		}

		myGrade(){
			alert('I am at grade'+ this.grade);
		}
	}

# 游览器

JavaScript出现是为了能在游览器中运行。

## 游览器对象

### window
`window`对象充当全局作用域，而且表示游览器窗口。

属性：`innerWidth`,`innerHeight`获取游览器的内部宽高，即去除菜单栏/工具栏等，对应的`outerWidth`,`outerHeight`

### navigator
表示游览器的信息：

- navigator.appname
- navigator.appVersion
- navigation.language
- navigation.platform：操作系统
- navigation.userAgent:游览器设定的`User-Agent`字符串

**注意：**`navigation`的信息容易被修改，初学者喜欢针对不同的游览器编写不同的代码的时候使用`if`比如：

	var width;
	if (getIEVersion(navigator.userAgent) < 9) {
		width = document.body.clientWidth;

	} else {
        	width = window.innerWidth;

	}

可以这样，使用短路运算符：

	var width = window.innerWidth || document.body.clientWidth;

### screen
表示屏幕信息

- screen.width;
- screen.height;
- screen.colorDepth:返回颜色位数

### location

表示当前页面的URL信息

	location.protocol; // 'http'
	location.host; // 'www.example.com'
	location.port; // '8080'
	location.pathname; // '/path/index.html'
	location.search; // '?a=1&b=2'
	location.hash; // 'TOP'

加载一个新页面：`location.assign()`

重新加载：`location.reload()`

	if (confirm('重新加载当前页' + location.href + '?')) {
		location.reload();

	} else {
        	location.assign('/discuss'); // 设置一个新的URL地址

	}

### document
表示当前也页面，对象是整个DOM树的根节点。

- `title`属性：从`<title>XXX</title>`中读取，但是可以改变


查找DOM树的某个节点，常用根据ID，Tag

- `cookie`属性，获取当前页面的Cookie
	Cookie是由服务器发送的key-value标识符，因为HTTP协议是无状态的，但是服务器需要区分哪个用户发送过来的请求，就可以使用Cookie来区分。当一个用户登入成功后，服务器发送一个Cookie给游览器，此后游览器发送请求的时候在请求头上附加`Cookie`
	Cookie也可以存储网站的一些设置。

		document.cookie; //  'v=123; remember=true; prefer=zh'

JavaScript能读取到页面的Cookie，然而用户的登入信息通常也存在Cookie中，这样就造成巨大的安全隐患，而且HTML页面中引入第三方的JavaScript代码是允许的：比如

	<!-- 当前页面在wwwexample.com -->
	<html>
		<head>
		        <script src="http://www.foo.com/jquery.js"></script>
	        </head>
			    ...
	</html>

如果引入的第三方的JavaScript中存在恶意代码，则www.foo.com网站将直接获取到www.example.com网站的用户登录信息。

**解决**服务器在设置Cookie的时候使用`httpOnly`，这样Cookie就不能被js代码读取了。这个需要游览器实现

### history
游览器的历史记录

`history`对象的`back()`,`forward()`,相当于游览器的后退和前进。

对于现代的Web网页来说不建议使用这个对象，直接后退或者前进。

## 操作DOM

HTML文档被游览器解析后就是一颗DOM树，改变HTML的结构需要通过JavaScript。

DOM是树形结构，操作DOM节点操作：

- 更新
- 遍历
- 添加
- 删除

操作DOM节点前，需要通过各种方式获取DOM节点。

- `document.getElementById()`:唯一
- `document.getElementByTagName()`:可能返回一组DOM
- `document.getElementByClassName()`:CSS选择器

例子：

	var test = document.getElementById(`test`);

	var trs = document.getElementByTd(`test-table`).getElementsByTagName(`tr`);

	var reds = document.getElementById(`test-div`).getElementsByClassName(`red`);

	var cs = test.children;

	// 获取test下第一个和最后一个字节点
	var first = test.firstElementChild;
	var last = test.lastElementChild;

方法二使用`querySelector()`,`querySlectorAll()`,需要了解selector语法：

	var q1 = document.querySelector(`#q1`);
	var ps = q1.querySelector(`div.hidhlighted > p`);

上面的DOM机节点指Element，但是DOM节点实际上是Node

hTML中Node包括：element，comment，CDATA_SECTION，Document等多种

### 更新DOM

直接修改节点的文本，方法有两种：

1. 修改`innerHTML`,不但可以修改DOM节点的文本内容，还可以直接通过HTML片段修改DOM节点内部的子树：

		// 获取<p id="p-id">....</p>
		var p = document.getElementById('p-id');
		// 设置文本为abc
		p.innerHTML = 'ABC'; // <p id="p-id">ABC</p>
		// set HTML
		p.innerHTML = 'ABC <span style="color:red">RED</span> XYZ';
		// <p>...</p>的内部结构已修改

	用innerHTML时要注意，是否需要写入HTML。如果写入的字符串是通过网络拿到了，要注意对字符编码来避免XSS攻击。

2. 修改'innerText','textContent'属性，这样可以自动对字符串进行HTML编码，保证无法设置任何HTML标签：

		// 获取<p id="p-id">...</p>
		var p = document.getElementById('p-id');
		// 设置文本
		p.innerText = '<script>alert("HI")</scropt>';
		// HTML 被自动编码，无法设置一个<script>节点
		// <p id="p-id">&lt;script&gt;alert("Hi")&lt;/script&gt;</p>

	两者的区别在于读取属性时，innerText不返回隐藏元素的文本，而textContent返回所有文本。另外注意IE<9不支持textContent。

修改CSS：

	// 获取<p id="p-id">...</p>
	var p = document.getElementById('p-id');
	// 设置CSS:
	p.style.color = '#ff0000';
	p.style.fontSize = '20px';
	p.style.paddingTop = '2em';


### 插入DOM

1. `appendChild`,把一个子节点添加到父节点的最后一个节点

		var js = document.getElementById('js');
		var list = document.getElementById('list');
		list.appendChild(js);

		<!-- HTML结构 -->
		<p id="js">JavaScript</p>
		<div id="list">
    			<p id="java">Java</p>
    			<p id="python">Python</p>
    			<p id="scheme">Scheme</p>
		</div>

		<!-- HTML结构 -->
		<div id="list">
   			 <p id="java">Java</p>
   			 <p id="python">Python</p>
   			 <p id="scheme">Scheme</p>
  			  <p id="js">JavaScript</p>
		</div>

		// 创建一个节点
		var haskell = document.createElement('p');
		haskell.id = 'haskell';
		haskell.innerText = 'Haskell';
		list.appendChild(haskell);

		// 动态创建一个style节点并加到head

		var d = document.createElement('style');
		d.setAttribute('type', 'text/css');
		d.innerHTML = 'p { color: red }';
		document.getElementsByTagName('head')[0].appendChild(d);

2. ‘insertBefore'

	parentElement.insertBefore(newElement, referenceElement);

字节点会插入到`referenceElement`之前。

	var
		list = document.getElementById('list'),
		ref = document.getElementById('python'),
		haskell = document.createElement('p');
	haskell.id = 'haskell';
	haskell.innerText = 'Haskell';
	list.insertBefore(haskell, ref);

插入的重点是获取到参考节点：

	var
	    i, c,
	    list = document.getElementById('list');
	for (i = 0; i < list.children.length; i++) {
	    c = list.children[i]; // 拿到第i个子节点
	}

### 删除DOM

需要获取需要删除的节点和其父节点

	var self = document.getElementById('to-be-removed');
	var parent = self.parentElement;
	var removed = parent.removeChild(self);
	removed == self; //true

**注意：**删除后的节点虽然不在文档树中，但实际还是在内存中的，可以随时被添加到别处。

在遍历一个父节点的时候，要注意，`children`属性是一个只读属性，并且实时更新。

## 操作表单

JavaScript操作表单和操作DOM类似。

HTML表单的输入控制主要有：

- 文本框，`<input type="text">`
- 口令框, `<input type="password"`>
- 单选框，`<input type = "radio">`
- 复选框，`<input type = "checkbox>"`
- 下拉框，`<select>`
-  隐藏文本，`<input type="hidden>"`,用户不可见，但是表单提交的时候会把隐藏文本发送到服务器

### 获取值
先获取`<input>`节点的引用，然后直接调用`value`获取对应的值

	var input = document.getElementById(`email`);
	input.value; // 用户输入的值

上面对饿方法适合于：`text`,`password`,`hidden`,`select`.

对于：单选框，复选框，`value`属性的返回值永远是html预设的值，所以需要`checked`

	// <label><input type="radio" name="weekday" id="monday" value="1"> Monday</label>
	// <label><input type="radio" name="weekday" id="tuesday" value="2"> Tuesday</label>
	var mon = document.getElementById('monday');
	var tue = document.getElementById('tuesday');
	mon.value; // '1'
	tue.value; // '2'
	mon.checked; // true或者false
	tue.checked; // true或者false

### 设置值

对于：`text`,`password`,`hidden`,`select`直设置`value`属性：

	var input = document.getElementById(`email`);
	input.value = `test@example.com`;

对于：`radio`,`checkbox`,设置`checked`属性

### HTML5控件

HTML5新增大量标准控件，常用的有：`date`,`datetime`,`datetime-local`,`color`..,都是使用`<input>`标签：

	<input type="date" value="2015-07-01">

### 提交表单

JavaScript有两种方式来处理表单的提交：

1. 通过`<form>`的`submit()`方法提交表单

		<form id="test-form">
			<input type="text" name="test">
			<button type = "button" onclick="doSubmitForm()">submit</button>
		</form>

		function doSubmitForm(){
			var form = document.getElementById('test-form');
			// ... 修改form的input
			form.submit();
		}
		</script>

	缺点：扰乱了游览器对form的正常提交

2. 响应`<form>`本身的`onsubmit`事件，在提交form时做修改：

		<!-- HTML -->
		<form id="test-form" onsubmit="return checkForm()">
		    <input type="text" name="test">
		    <button type="submit">Submit</button>
		</form>

		<script>
		function checkForm() {
		    var form = document.getElementById('test-form');
		    // 可以在此修改form的input...
		    // 继续下一步:
		    return true;
		}
		</script>

	**注意：**`return true`来告诉游览器继续提交，如果`return false`，游览器不会继续提交form，这种通常是用户输入有误。

### 操作文件

在HTML表单中可以上传文件的唯一控件就是`<input type="file">`

**注意：**当一个表单包含`<input type="file">`,表单的`enctype`必须指定为`multipart/form-data`,`method`必须指定为`post`

通常文件都是由后台服务器处理的，JavaScript可以在提交表单的时候对文件的扩展名进行检查：

	var f = document.getElementById(`test-file-upload`);
	var filename = f.value;
	if (!filename || !(filename.endsWith('.jpg') || filename.endsWith('.png') || filename.endsWith('.gif'))) {
    alert('Can only upload image file.');
    return false;
	}

### File API
由于JavaScript对用户上传的文件操作非常有限，尤其是无法读取文件内容，使得很多需要操作文件的网页不得不用Flash这样的第三方插件来实现。

随着HTML5的普及，新增的File API允许JavaScript读取文件内容，获得更多的文件信息。

HTML5提供`File`,`FileReader`两个对象来操作文件。

	var
	    fileInput = document.getElementById('test-image-file'),
	    info = document.getElementById('test-file-info'),
	    preview = document.getElementById('test-image-preview');
	// 监听change事件:
	fileInput.addEventListener('change', function () {
	    // 清除背景图片:
	    preview.style.backgroundImage = '';
	    // 检查文件是否选择:
	    if (!fileInput.value) {
	        info.innerHTML = '没有选择文件';
	        return;
	    }
	    // 获取File引用:
	    var file = fileInput.files[0];
	    // 获取File信息:
	    info.innerHTML = '文件: ' + file.name + '<br>' +
	                     '大小: ' + file.size + '<br>' +
	                     '修改: ' + file.lastModifiedDate;
	    if (file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
	        alert('不是有效的图片文件!');
	        return;
	    }
	    // 读取文件:
	    var reader = new FileReader();
	    reader.onload = function(e) {
	        var
	            data = e.target.result; // 'data:image/jpeg;base64,/9j/4AAQSk...(base64编码)...'
	        preview.style.backgroundImage = 'url(' + data + ')';
	    };
	    // 以DataURL的形式读取文件:
	    reader.readAsDataURL(file);
	});

### 回调

单线程执行模式

在JavaScript中，执行多任务实际上都是异步调用，比如上面的代码：

	reader.readAsDataURL(file);

回调：

	reader.onload = function(e) {
	    // 当文件读取完成后，自动调用此函数:
	};

## AJAX
不是JavaScript的规范

Asynchronous JavaScript and XML，意思就是用JavaScript执行异步网络请求。

Web的运作原理：一次HTTP请求对应一个页面。

如果要让用户留在当前页面中，同时发出新的HTTP请求，就必须用JavaScript发送这个新请求，接收到数据后，再用JavaScript更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

在现代浏览器上写AJAX主要依靠XMLHttpRequest对象：

对于低版本的IE，需要换一个ActiveXObject对象：

如果你想把标准写法和IE写法混在一起，可以这么写：

	var request;
	if (window.XMLHttpRequest) {
	    request = new XMLHttpRequest();
	} else {
	    request = new ActiveXObject('Microsoft.XMLHTTP');
	}

通过检测window对象是否有XMLHttpRequest属性来确定浏览器是否支持标准的XMLHttpRequest。注意，不要根据浏览器的navigator.userAgent来检测浏览器是否支持某个JavaScript特性，一是因为这个字符串本身可以伪造，二是通过IE版本判断JavaScript特性将非常复杂。

### 安全限制

这是因为浏览器的同源策略导致的。默认情况下，JavaScript在发送AJAX请求时，URL的域名必须和当前页面完全一致。

完全一致：域名相同，协议相同，端口号相同

那是不是用JavaScript无法请求外域（就是其他网站）的URL了呢？方法还是有的，大概有这么几种：

1. 通过Flash插件发送HTTP请求，这种方式可以绕过浏览器的安全限制，但必须安装Flash，并且跟Flash交互。不过Flash用起来麻烦，而且现在用得也越来越少了。
2. 通过在同源域名下架设一个代理服务器来转发，JavaScript负责把请求发送到代理服务器：

		'/proxy?url=http://www.sina.com.cn'

	代理服务器再把结果返回，这样就遵守了浏览器的同源策略。这种方式麻烦之处在于需要服务器端额外做开发。
3. JSONP，它有个限制，只能用GET请求，并且要求返回JavaScript。这种方式跨域实际上是利用了浏览器允许跨域引用JavaScript资源：

### CORS
如果浏览器支持HTML5，那么就可以一劳永逸地使用新的跨域策略：CORS了。

Cross-Origin Resource Sharing，是HTML5规范定义的如何跨域访问资源。

![](http://www.liaoxuefeng.com/files/attachments/00143640805071744d58164a40e42ef92b9973824451595000/l)

## Promise

“承诺将来会执行”的对象在JavaScript中称为Promise对象。

![](http://www.liaoxuefeng.com/files/attachments/001436512391628944d5da9a5654a35b0ace38246f30b9c000/l)

要串行执行这样的异步任务，不用Promise需要写一层一层的嵌套代码。有了Promise，我们只需要简单地写：

	job1.then(job2).then(job3).catch(handleError); //其中，job1、job2和job3都是Promise对象。

	var p1 = new Promise(function (resolve, reject) {
	    setTimeout(resolve, 500, 'P1');
	});
	var p2 = new Promise(function (resolve, reject) {
	    setTimeout(resolve, 600, 'P2');
	});
	// 同时执行p1和p2，并在它们都完成后执行then:
	Promise.all([p1, p2]).then(function (results) {
	    console.log(results); // 获得一个Array: ['P1', 'P2']
	});

	var p1 = new Promise(function (resolve, reject) {
	    setTimeout(resolve, 500, 'P1');
	});
	var p2 = new Promise(function (resolve, reject) {
	    setTimeout(resolve, 600, 'P2');
	});
	Promise.race([p1, p2]).then(function (result) {
	    console.log(result); // 'P1'
	});

## Canvas
