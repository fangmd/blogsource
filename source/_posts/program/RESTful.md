---
title: RESTful
date: 2016-08-07 13:18:12
tags: [API]
category: Program

---

>URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作

# REST

Representational State Transfer,表现层状态转化

符合REST原则的架构：RESTful架构

## REST 架构约定

1. CS结构（Client–server）

    客户端是一个相对独立的实现，它不必考虑数据的持久化存储问题。服务端拥有和保存数据，服务端不去关心客户端内部实现，也不用关心客户端请求的上下文。服务端和客户端之间遵守相同的接口规范。在遵守相同接口规范的前提下，二者都可以独立演化，甚至可以被其它的实现替代。

2. 无状态（Stateless）

    服务端不依赖前后不同请求的顺序和状态信息来完成请求.
    请求的session信息由客户端持有，并在必要时连同请求数据一起发送。服务端可以使用请求中的session信息去其它外部服务或者数据库获取相关内容进以便对该请求做权限验证等操作。

    如果所需数据要通过多次请求才能完成，客户端必须自己负责记录状态（因为服务器不跟踪客户端的状态）

    服务端不依赖客户端的请求顺序和状态提高了服务器的可扩展性（scalability）

    无状态的约定也提高了系统的健壮性（reliability）

3. 缓存机制（Cacheable）

    服务端应该明确规定返回数据的缓存机制，包括是否可缓存，缓存如何失效以及利用缓存获取增量数据而不必每次获取全部数据等。合理的缓存设计可以减少请求次数，进而提高服务器的效率和性能。

4. 系统分层（Layered system）

    客户端不用知道数据是从服务端直接返回还是通过中转代理返回。

5. 可定制代码（可选）（Code on demand）

6. 一致的接口（Uniform interface）

## 主语 资源（Resources）

"表现层"其实指的是"资源"（Resources）的"表现层"。

资源表示网络上或者说服务器上的具体的数据：图片，视频，json数据；使用URI指向这个数据

## 表现层（Representation）

比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。

URI应该只代表"资源"的位置。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定

## 状态转化（State Transfer）

如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）

四种基本操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源。


## 总结：

1. 每一个URI代表一种资源；
2. 客户端和服务器之间，传递这种资源的某种表现层；
3. 客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。

## 设计误区

### URI包含动词
因为"资源"表示一种实体，所以应该是名词，URI不应该有动词，动词应该放在HTTP协议中。

比如：某个URI是/posts/show/1，其中show是动词，这个URI就设计错了，正确的写法应该是/posts/1，然后用GET方法表示show。

### 动作
如果某些动作是HTTP动词表示不了的，你就应该把动作做成一种资源。

比如网上汇款，从账户1向账户2汇款500元，错误的URI是：

    POST /accounts/1/transfer/500/to/2

正确的写法是把动词transfer改成名词transaction，资源不能是动词，但是可以是一种服务：

    POST /transaction HTTP/1.1
    Host: 127.0.0.1
　　
    from=1&to=2&amount=500.00

### URI中加入版本号

因为不同的版本，可以理解成同一种资源的不同表现形式，所以应该采用同一个URI。

Accept字段中进行区分：

    Accept: vnd.example-com.foo+json; version=1.0
    Accept: vnd.example-com.foo+json; version=1.1
    Accept: vnd.example-com.foo+json; version=2.0



注意使用json传输的时候，要求请求头里面加入：Content-Type：applicatin/json.否则抛出415异常（unsupported media type）



# 接口设计

符合REST设计风格的Web API称为RESTful API

## API 命名应该简洁明了

所有 API 应该使用 REST 架构约定形式命名：将 API 请求对象看成一个个资源，实现者使用相应的 HTTP 的动词（GET, POST, PUT, PATCH, DELETE）来访问和操作这些资源。

案例：

    - GET /users  获取所用户
    - GET /user/1234  获取ID为1234的用户
    - POST /users  创建一个新用户
    - PUT /users/1234  更新ID为1234的用户
    - PATCH /users/1234  更新ID为1234的用户的部分内容
    - DELETE /users/1234  删除ID为1234的用户

>约定：使用了复数形式

如何表示资源之间的联系 API：

例子：

    - GET /users/1234/comments  获取用户ID为1234的所有评论
    - GET /users/1234/comments/1 获取用户ID为1234的评论ID为1的单个评论
    - DELETE /users/1234/messages/1  删除用户评论ID为1，属于用户1234的单个评论

## 考虑到系统迭代和兼容性，URL中添加版本号

    http://api.domain.com/v2

接口不可能一成不变，在不停迭代中，总会发生变化。接口的变化一般会有几种：

- 数据的变化，比如增加了旧版本不支持的数据类型
- 参数的变化，比如新增了参数
- 接口的废弃，不再使用该接口了

为了适应这些变化，必须得做接口版本的设计。

如果一个 API 的版本过期了，任何把该请求重定向到最新版本上。

## 优雅的设计条件过滤，排序，搜索等传入参数形式

输入参数推荐采用 HTTP Query Parameter 的方式实现。

例子：

- 比如你要设计一个API，返回所有已经登录的用户，可以这样做:

        GET /users?login=true

- 获取所有的用户，返回结果按照create_at降序排序可以这样设计：

        GET /users?sort=-create_at

- 组合使用过滤条件和排序：

        GET /users?sort=-create_at,login_at&login=true

- 对于一些常用的条件搜索和过滤，可以考虑映射到一个新的API（相当于快捷方式）比如设计一个用于返回最近登录用户的API：

        GET /users/recently_login

    这种设计可以简化客户端的调用，否则调用者每次都要根据时间合成 Query Parameter，增加了客户端使用复杂度。

- 查询数据的部分内容：

        GET /user?fields=id,user_name,address&diabled=false&sort=-login_at
        GET /facebook/v2.8/me?fields=id,name,birthday,cover,devices,email&access_token=xxx

## 合理设计返回数据的形式，格式和考虑启用压缩（gzip）

## 根据不同的 API 操作，设置合适的 HTTP 状态码和必要的出错信息

对于需要提供额外说明的错误类型，可以在 HTTP Body 中详细描述，便于调用者排查原因：

    {
     "error": {
      "message":"Message describing the error",
      "type":"OAuthException",
      "code":190,
      "error_subcode":460,
      "error_user_title":"A title",
      "error_user_msg":"A message",
      "fbtrace_id":"EJplcsCHuLu"
      }
    }

## 使用 token 机制设计鉴权和验证系统（Authorization and Authentication）

### 一 针对特定用户生成一个 token
每次API的调用请求都带上这个 token。为了防止 token 泄露引发的安全问题，还应该考虑 token 什么时候失效，什么时候需要重新生成。

### OAuth 2

OAuth 2 适合需要把某一资源暴露给第三方应用的情况

比如：

    如果你使用新浪微博登录豆瓣，在你的同意下（你在微博的登录界面输入用户名密码，并且确认），微博最终会给豆瓣一个具有实效性的 token，豆瓣凭借这个 token 来读取你的昵称和头像信息。

### 方案

1. 用户使用户名密码或者第三方登录，最终请求一个我们设计的登录 API（这个 API 接受用户名密码，或第三方登录验证结果）；
2. 服务端认证成功以后，生成一个 token，并将这个 token 和用户信息关联在一起，同时返回这个 token 给调用客户端；
3. 客户端记录并保存下这个 token；
4. 下次客户端发起和用户相关请求 API 都要在 http header 中带上这个 token；
5. 服务端通过这个 token 去区分用户是谁，判断这个用户是否已经登录和有什么样的权限；
6. 服务端也要考虑 token 的失效时间；
7. 客户端在发现 token 失效的时候重新请求新的 token

为什么使用 token 而不是直接把用户名和密码放在 http header 中直接做授权和验证？

>原因是调用 API 一般会被频繁调用，这样用户名和密码频繁在网络上传输，增加了泄漏的危险。如果使用token，即使泄漏了也不会暴露用户的密码，何况 token 也被经常被设计成有时间限制的，超时以后当前 token 就会失效，需要客户端重新做验证获得新的 token，暴露之后的影响很快就会过去。

## 如何实现数据的分页返回


## 如何处理有关联资源的返回数据

情况：

>有一个 API，输入一个指定用户 id，返回一个该用户所有评论信息。最终要在 UI 上显示的，除了该用户评论的具体文本内容以外，还有用户名，头像，个人简介之类和该用户相关的详细信息。

应该设计的 api：

    /comments?user=1234

    {
      data: [
       {comment:"RESTful Service API"},
       {comment:"J:"},
       ...
     ],

     comment_user: {user_id: "1234", avatar: "a.jpg", nickName:"Jeffrey"...}
    }

## 考虑启用 HTTP 缓存机制

HTTP协议本身支持两种缓存机制: ETag 和 Last-Modified。

1. ETag：HTTP 请求中在 header 中包含一个内容的 hash，如果返回结果没有变化，该请求会直接返回304 Not Modified，而不是所有数据内容本身
2. Last-Modified: 和 Etag 工作原理差不多，只是使用时间戳作为内容是否过期的标志。

Nginx：[A Guide to Caching with NGINX and NGINX Plus](https://www.nginx.com/blog/nginx-caching-guide/)

## 限制 API 调用频次（Rate limiting）

## 尽可能的使用 HTTPS，涉及用户验证的 API 一定要强制启用 HTTPS

如果你的WEB Server 是 Nginx，在部署了 HTTPS 的情况下，下面两个选项务必仔细设置，因为这个两个简单的设置可以很大程度上避免一些安全问题:

1. ssl_prefer_server_ciphers： 表示服务端加密算法优先于客户端加密算法，主要是[防止降级攻击 （downgrade attack）](https://en.wikipedia.org/wiki/Downgrade_attack)
2. Strict-Transport-Security（HSTS）：告诉浏览器这个域名在指定的时间（max-age）内应该强制使用 HTTPS 访问。

## Url design tricks

### Url是区分大小写的

- /Posts
- /posts

指向不同的地址

### Back forward Slash (/)

目前比较流行的API设计方案，通常建议url以 `/` 作为结尾,如果API `GET`请求中，url不以`/`结尾，则重定向到以`/`结尾的API上去（这点现在的web框架基本都支持），因为有没有 `/`，也是两个url

### 连接符 - 和 下划线 _

ESTful API 应具备良好的可读性，当url中某一个片段（segment）由多个单词组成时，建议使用 `-` 来隔断单词，而不是使用 `_`

原因：浏览器中超链接显示的默认效果是，文字并附带下划线，如果API以_隔断单词，二者会重叠，影响可读性。

# 服务器返回的数据格式

    {
        code：0,
        message: "success",
        data: { key1: value1, key2: value2, ... }
    }

- code: 返回码，0表示成功，非0表示各种不同的错误
- message: 描述信息，成功时为"success"，错误时则是错误信息
- data: 成功时返回的数据，类型为对象

code错误参考：

- 0：成功
- 100：请求错误
- 101：缺少appKey
- 102：缺少签名
- 103：缺少参数
- 200：服务器出错
- 201：服务不可用
- 202：服务器正在重启

错误信息一般有两种用途：

1. 是客户端开发人员调试时看具体是什么错误；
2. 是作为App错误提示直接展示给用户看。


参考：

- [http://www.ruanyifeng.com/blog/2011/09/restful.html](http://www.ruanyifeng.com/blog/2011/09/restful.html)
- [https://bourgeois.me/rest/](https://bourgeois.me/rest/)
- [http://keeganlee.me/post/architecture/20160107](http://keeganlee.me/post/architecture/20160107)
- [http://blog.jimmylv.info/2015-11-11-what-is-really-rest/](http://blog.jimmylv.info/2015-11-11-what-is-really-rest/)
- [http://www.jianshu.com/p/cf80d644727e](http://www.jianshu.com/p/cf80d644727e)
- [http://blog.igevin.info/posts/restful-api-get-started-to-write/](http://blog.igevin.info/posts/restful-api-get-started-to-write/)
