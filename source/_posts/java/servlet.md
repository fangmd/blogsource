---
title: Servlet
date: 2016-03-25 21:18:12
tags: java基础
category: java基础
---

## Servlet
>运行在服务器端的web server中的java小程序

web server:web服务器，常见的有：tomcat、jboss、websphere、iis等jsp引擎

tomcat、jboss：纯粹使用java语言编写的，将服务端(ServerSocket)的共通的代码抽取出来，做成一个产品。

网站与web server的关系，web服务器中可以部署n个网站。

Servlet的生命周期：-->底层使用了单例设计模式  出生--提供服务(deGet(),doPost())--销毁(服务器重启，关闭)

url:统一资源定位符。用来标示互联网上资源的位置。(http://xxx)

uri:统一资源标识符。url是uri的子集。(ftp://xxx)


<!--more-->

### Servlet的跳转方式
1. 客户端跳转
	1. 地址栏发生改变
	2. 不会传递request对象
	3. response.sendRedirect("地址")

2. 服务端跳转
	1. 地址栏不会发生变化
	2. 能传递request对象
	3. request.getRequestDispatcher("目的页面地址").forward(resp,req);

验证：模拟用户登入到新浪邮箱的情形

页面：

1. 登入界面(html)
2. 登入成功界面
3. 登入失败界面

### 字符集乱码解决
表单提交方式不同，字符集处理乱码问题的解决方案也不同

1. get提交方式
	1. 方案一：硬编码方式--常用方式但是不推荐：

			//req.setCharacterEncoding("UTF-8"); 不需要
			String name = req.getParameter("name");//iso-8859-1
			String name = new String(name.getBytes("iso-8859-1"),"UTF-8");
			response.setContentType("text/html;charset=UTF-8");
	2. 方案二：配置文件修改方式（推荐）

			<Connector connectionTimeout="20000" URIEncoding="UTF-8"
			disableUploadTimeout="false" executor="tomcatThreadPool" 
			port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
2. post提交方式

		req.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");//必须

get与post解决乱码的区别：

1. 硬编码：get需要对name转码从"ISO-8859-1"-"UTF-8";有两种方式
	1. 使用`URLEncoder`，`URLDecoder`
	2. `new String(name.getBytes("iso-8859-1"),"UTF-8");`
	3. 两种方法都需要`response.setContentType("text/html;charset=UTF-8");//必须`
2. get可以修改配置文件

get与post提交数据区别：

1. get的URL包含了数据：所以数据需要以“？”开始
2. post的URL不包含数据：所以数据不需要“？”开始

URL字符类：**查看底层源码的实现**

		name = URLEncoder.encode(name,"ISO-8859-1");
		name = URLDecoder.decode(name, "UTF-8");

## servlet服务器端
**涉及的api**

1. HttpServlet类
	1. doGet(HttpServletRequest req, HttpServletResponse resp)
	2. dePost(HttpServletRequest req, HttpServletResponse resp)
 
### 服务器编程步骤：
1. 创建一个web工程(注意选择 generate web.xml：自动生存xml入口文件)
2. webConent根目录下创建一个`index.html`
3. 创建一个index对应的java文件
4. 修改web.xml文件
	
		<servlet>
			<servlet-name>LoginServlet</servlet-name>
			<servlet-class>com.doublefang.servlet.LoginServlet</servlet-class>
		</servlet>
		<servlet-mapping>
 			<servlet-name>LoginServlet</servlet-name>
			<url-pattern>/LoginServlet</url-pattern>
		  </servlet-mapping>

---
## servlet客户端编程方法一：HttpURLConnection
**涉及的api**

URL类：代表一个统一资源定位符，指向互联网资源的指针
	
1. URL(String spec):通过参数指定的url地址构建对象
2. URLConnection openConnection():构建客户端到服务器连接的对象

URLConnection类：代表应用程序和URL之间的通信链接

1. InputStream getInputStream():从网络上获得数据(网络到本地客户端的通路)
2. OnputStream getOutputStream():客户端向远程服务器传递数据(客户端到网络的通路)
3. void setDoInput(boolean doinput):将服务器到客户端的通路设置为闭合或者通畅(默认是true)
4. void setDoOuput(boolean doouput):将客户端到服务器的通路设置为闭合或者通畅(默认是false)

URLConnection的子类HttpURLConnection:

1. 常用字段
	1. static int HTTP_OK:客户端发送的数据服务器已经处理完毕，并给出了正常的反馈
	2. static int HTTP_NOT_FOUND:客户端请求的服务端的资源为找到

需求：从本地服务器下载和上传图片

前提：

1. 在本地服务器相应网站下准备资源
2. 启动服务器，使其处于运行状态

### 客户端编程步骤：
#### 下载图片
1. 将网址封装成URL对象
2. 构建连接获取对象HttpURLConnection
3. 获取网络字节输入流对象
4. 构建FileOutputStream对象(将图片下载到本地)
5. 资源释放

  使用HttpURLConnection模拟浏览器实现用户登录
#### 模拟登入
实现方式一：get请求方式

1. 准备数据
2. 构建URL的对象
3. 构建HttpURLConnection对象
4. 获得来自服务器端的反馈，并显示出来
5. 资源的释放

实现方式二：post请求方式

1. 准备数据
2. 构建URL的对象
3. 构建HttpURLConnection对象
4. 网络输出流OutputStream对象的获取,用来将客户端的数据发送给服务器去处理
5. 获得来自服务器端的反馈，并显示出来
6. 资源的释放

**插曲**：接口回调回顾

1. 定义一个接口
2. 在需要使用这个接口的地方，创建一个匿名接口实现类，重写里面的抽象方法，并直接回调该方法
		
		new InterfaceDemo(){
			public void login(String name, String pwd) {
					userLogin(name, pwd);
			}
		}.login(name, pwd);

## servlet客户端编程方法二：HttpClient
### HttpClient介绍
>由Apache开源组织开发的一套用户替代HttpURLConnection的工具包

### HttpClient主要API
1. HttpClient:客户端(对游览器客户端的抽取和封装，与Socket；类似)(接口)
2. DefaultHttpClient是HttpClient的实现类
	1. HttpResponse execute(HttpUriRequest):向服务器发送请求
3. HttpResqonse:服务端给客户端发送反馈数据的抽取和封装（接口）
	1. HttpEntity getEntity():获取服务器端反馈的具体数据

4. HttpEntity:接口
	1. getContent():获取网络输入流
	2. writeTo(OutputStream):将数据写入到参数指定的输出流中

5. EntityUtils：工具类，用来操作HttpEntity
	1. EntityUtils.toString(HttpEntity):直接将参数HttpEntity对象中的数据以字符串的形式取出

6. HttpUriRequest：对客户端请求方式的抽取(接口)
	7. HttpPost:对post请求方式的抽取和封装(类)
		1. HttpPost(String):通过参数指定的url的字符串构建对象

	8. HttpGet:对get请求方式的抽取和封装(类)
		1. HttpGet(String):通过参数指定的url的字符串构建对象


需求：

使用HttpClient从本地服务器上下载图片。

1. 客户端实例的构建（HttpClient）
2. 请求方式实例的构建(HttpGet)
3. 发送请求，获得反馈的数据
4. 分析反馈的数据，将其写入本地文件中
5. 资源释放

使用HttpClient模拟浏览器实现用户登录

方式①：get方式步骤：

1. HttpClient客户端对象的构建
2. 准备登录的数据
3. HttpGet请求方式实例的构建
4. 发送请求
5. 处理来自服务器端的反馈
6. 资源释放

方式②：post方式步骤：

1. HttpClient客户端对象的构建
2. 准备登录的数据
3. HttpPost请求方式实例的创建
4. 将数据设置到HttpPost对象中

			// 4、将数据设置到HttpPost对象中
			// ①将请求的数据封装成HttpEntity的实例
			// ②设置到HttpPost中
			List<NameValuePair> parameters = new LinkedList<>();
			// 将用户名设置到NameValuePair实例中
			NameValuePair nameParam = new BasicNameValuePair("name", name);
			// 将密码设置到NameValuePair实例中
			NameValuePair pwdParam = new BasicNameValuePair("pwd", pwd);
			// 将名值对添加到容器中
			Collections.addAll(parameters, nameParam, pwdParam);

			HttpEntity entity = new UrlEncodedFormEntity(parameters, "UTF-8");
			post.setEntity(entity);
5. 向服务器发送请求
6. 获取来自服务器端的反馈，并显示
7. 资源的释放

HttpClient类与HttpURLConnection类的区别：
HttpClient：一套api更加面向对象，思路比较清晰。