---
title: Servlet
date: 2015-12-28 21:18:12
tags: [java,day]
categories: java基础
---

##Servlet
>运行在服务器端的web server中的java小程序

web server:web服务器，常见的有：tomcat、jboss、websphere、iis等jsp引擎

tomcat、jboss：纯粹使用java语言编写的，将服务端(ServerSocket)的共通的代码抽取出来，做成一个产品。

网站与web server的关系，web服务器中可以部署n个网站。

Servlet的生命周期：-->底层使用了单例设计模式  出生--提供服务(deGet(),doPost())--销毁(服务器重启，关闭)

url:统一资源定位符。用来标示互联网上资源的位置。(http://xxx)

uri:统一资源标识符。url是uri的子集。(ftp://xxx)

###Servlet的跳转方式
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

###字符集乱码解决
表单提交方式不同，字符集处理乱码问题的解决方案也不同

1. get提交方式
	1. 方案一：硬编码方式--常用方式但是不推荐：

			req.setCharacterEncoding("UTF-8");
			String name = req.getParameter("name");//iso-8859-1
			String name = new String(name.getBytes("iso-8859-1"),"UTF-8");
	2. 方案二：配置文件修改方式（推荐）

			<Connector connectionTimeout="20000" URIEncoding="UTF-8"
          disableUploadTimeout="false" executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
2. post提交方式

		req.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");
###servlet服务器端涉及的api
1. HttpServlet类
	1. doGet(HttpServletRequest req, HttpServletResponse resp)
	2. dePost(HttpServletRequest req, HttpServletResponse resp)

服务器编程步骤：
###servlet客户端编程涉及的api
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

客户端编程步骤：

1. 将网址封装成URL对象
2. 构建连接获取对象HttpURLConnection
3. 获取网络字节输入流对象
4. 构建FileOutputStream对象(将图片下载到本地)
5. 资源释放

