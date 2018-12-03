---
title: Servlet
date: 2016-03-27 21:18:12
tags: [java]
category: java基础
---

# Servlet

>运行在服务器端的web server中的java小程序

web server:web服务器，常见的有：tomcat、jboss、websphere、iis等jsp引擎

tomcat、jboss：纯粹使用java语言编写的，将服务端(ServerSocket)的共通的代码抽取出来，做成一个产品。

网站与web server的关系，web服务器中可以部署n个网站。

Servlet的生命周期：-->底层使用了单例设计模式  出生--提供服务(deGet(),doPost())--销毁(服务器重启，关闭)


<!--more-->

servlet 规范，三个技术点：

1. servlet 技术
2. filter 过滤器
3. listener 监听器


# servlet服务器端

1. Servlet: 接口
2. HttpServlet 类: 接口
	1. doGet(HttpServletRequest req, HttpServletResponse resp)
	2. dePost(HttpServletRequest req, HttpServletResponse resp)

生命周期：网页访问的时候，Servlet 对象初始化，服务器关闭的时候销毁


## Servlet 配置

1. 基本配置

servlet 类配置，servlet 虚拟路径配置

路径匹配：完全匹配(`/aaa/bbb`)，目录匹配(`/aaa/*`), 扩展名匹配(`*.zip`)

>目录匹配和扩展名匹配不能混合使用 /aaa/bbb/*.zip (错误)

2. 服务器启动实例化 servlet 配置

```
<servlet>
	<servlet-name></servlet-name>
	<servlet-class>com.passon.HelloServlet</servlet-class>
	<!-- 在服务器启动的时候就创建 Servelet, 数字代表优先级 越小越高-->
	<load-on-startup>3</load-on-startup>
</servlet>

```

3. 缺省 Servlet

url-pattern 配置成 `/`, 表示默认 Servlet（配置后静态资源就不能访问了）

当访问的资源地址，所有的 Servlet 都不匹配的时候，使用 缺省 Servlet;

>静态资源，jsp 都是走 Servlet, tomcat 内部自定义的 Servlet 用于处理 静态资源 和 jsp

4. 欢迎页面

```
<welcome-file-list>

</welcome-file-list>
```

## HttpServlet

HttpServlet 父类 的 父类 就是 Servlet.class.

## 服务器编程步骤：

1. 创建一个web工程(注意选择 generate web.xml：自动生存xml入口文件)
2. webConent根目录下创建一个`index.html`
3. 创建一个index对应的java文件
4. 修改web.xml文件
	
```
		<servlet>
			<servlet-name>LoginServlet</servlet-name>
			<servlet-class>com.doublefang.servlet.LoginServlet</servlet-class>
		</servlet>
		<servlet-mapping>
 			<servlet-name>LoginServlet</servlet-name>
			<url-pattern>/LoginServlet</url-pattern>
		  </servlet-mapping>
```

# ServletContext 对象

ServletContext 对象：代表一个 Web 应用的环境，内部封装了 Web 应用的信息；一个 Web 应用只有一个 ServletContext 对象；生命周期和 Web 应用一样长；

获取 ServletContext 对象：`config.getServletContext()`, `this.getServletContext()`

作用：

1. 获取 Web 应用全局的初始化参数 web.xml `context-param`
2. 获取 Web 应用中任何资源的绝对路径: 

```
1.
context.getRealPath(资源相对Web应用的位置)

2.
String path = ContextServlet.class.getClassLoader().getResource("..相对于 classes 地址").getPath();
```

3. ServletContext 是一个域对象

```
域对象：存储数据的区域

可以用于不同 Servlet 之间数据交互。
context.setAttribute('key', value);
context.getAttribute(key);
context.removeAttribute(key);
```

# HttpServletResponse

设置 响应行，响应头，响应体

```
// 响应行 状态码
setStatus(statusCode);

// 响应头
addHeader(key, value)
addIntHeader(key, value)
addDateHeader(key, value)
setHeader(key, value)
setIntHeader(key, value)
setDateHeader(key, value)
通常使用 set, key 相同会覆盖

// 响应体
// 1. write String
PrintWriter writer = resp.getWriter();
writer.write("Hello response!!!");
// 2. 中文处理
resp.setCharacterEncoding("UTF-8");
// resp.setHeader("Content-Type", "text/html;charset=UTF-8");
resp.setContentType("text/html;charset=UTF-8");
PrintWriter writer = resp.getWriter();
writer.write("中国");
// 
```

## 重定向

1. 访问服务器两次。
2. 第一次返回重定向地址，第二次返回重定向的地址


方法一：

重定向实现：状态码 302, location

```
setStatus(302);

setHeader("location", "/new_address");
```

方法二：

```
sendRedirect("/new_address")
```

方法三：通过 定时刷新 实现

```
setHeader("refresh", "5;url=http://ww.baidu.com")
```

方法四：js 端实现跳转

```js
window.onload = function(){
	var time = 5;
	var secondEle = document.getElementById("second");
	var timer = setInterval(function(){
			secondEle.innerHTML = time;
			time--;
			if(time==0){
				clearInterval(timer);
				localtion.href = "http://www.baidu.com";
			}
		}, 1000);
}
```


## 游览器端文件下载

游览器可以解析的文件格式会直接打开，不会下载。

通过设置请求头，强制让游览器下载文件: 文件下载需要设置两个请求头

```
http://localhost:8080/download?filepath=imgs.jpg

@WebServlet("/download")
public class DownloadFileServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String filepath = req.getParameter("filepath");

        resp.setContentType(this.getServletContext().getMimeType(filepath));
        resp.setHeader("Content-Disposition", "attachment;filename=download.jpg");

        String realPath = getServletContext().getRealPath("imgs/"+filepath);
        FileInputStream is = new FileInputStream(realPath);
        ServletOutputStream os = resp.getOutputStream();

        int len = 0;
        byte[] buff = new byte[1024];
        while ((len = is.read(buff)) > 0) {
            os.write(buff, 0, len);
        }
        is.close();
        //os.close();//不需要手动关闭
    }


    @Override
    public void destroy() {
        super.destroy();
    }
}
```

下载的文件中文名乱码处理：

```
http://localhost:8080/download?filepath=中文名字.jpg

filename = new String(filename.getbytes("ISO8859-1"), "UTF-8");// 解析到正确的文件名

// 获取 user-agent
// 不同的游览器需要用不同的编码方式设置文件名
resp.setHeader("Content-Disposition", "attachment;filename="+ 编码后的中文.jpg);

```


## 其他

1. response 输出流不需要手动关闭
2. getWirter 和 getOutputStream 不能同时使用

# HttpServletRequest

```
获取请求方式: getMethod();

获取请求的资源:
getRequestURI(); // 所有资源：包含网络资源
getRequestURL(); // 网络资源
getContextPath();
getQueryString(); // 获取 URL 后面所有的参数，不常用

获取请求头:
getDateHeader(name)
getHeader(name) **
getHeaderNames() *
getHeaders(name)
getIntHeader(name)

获取请求体: POST
getParameter(name); **
getParameterValues(name);
getParameterNames();
getParameterMap(); *
```

>获取客户端 ip: request.getRemoteAddr();

## 请求转发

请求转发 与 重定向 区别：

- 请求转发：一次请求，服务器内部行为
- 重定向：二次请求，外部行为

```
dispatcher = request.getRequestDispatcher("/new_path");
dispatcher.forward(request, response);
```

请求转发的时候 request 可以作为请求域:

```
request.setAttribute(key, value);
request.getAttribute(key);
request.removeAttribute(key);
```

作用范围：一次请求中。

# 注册例子

BeanUtils: 将 Map 中数据映射到实体类中。

`UUID.randomUUID()`: 创建用户 uid;

表单输入中文乱码：

```
(只适合post方式)设置 request 的编码：request.setCharacterEncoding("UTF-8");

(get, post) String userName = request.getParameter("username");
userName = new String(userName.getBbytes(""iso885801), "UTF-8");

```



# Servlet的跳转方式

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

## 字符集乱码解决
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
