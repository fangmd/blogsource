---
title: JWT
date: 2019-09-26 13:18:12
tags: [JWT]
category: Server

---

# 是什么

JWT: JSON Web Token

1. 是一种规范，数据格式
2. 使用数字签名保证数据完整性，可以使用 HMAC 签名或者非对称加密 RSA ECDSA.

# 什么时候使用

1. Authorization: 最常用的方式，在请求头里添加 Authorization
2. Information Exchange: 数据传输

# JWT 数据结构

JWT 包含:

1. Header
2. Payload
3. Signature

中间用 `.` 分割: `xxxxx.yyyyy.zzzzz`

## Header

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

头经过 `Base64Url` 处理

## Payload

自定义数据存储位置, 包含3部分: registered, public, private

- Registered claims, 包含: `iss(issuer), exp(expiration time), sub(subject), aud(audience), other`

这里名称都是3个字母，为了简化。

- Public claims: 
- Private claims

例子:

```js
{
  "sub": "1234567890",
  "name": "Double",
  "admin": true
}
```

Payload 数据经过 `Base64URL` 处理

**这里的数据都是公开的所以不能存储私密数据**

## Signature

签名

```js
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

保证数据不会被修改。

# 优点

其他几种 token，SWT Simple Web Tokens, SAML Security Assertion Markup Language Tokens.

1. JSON 数据格式简介，支持的语言更多同时在 JS 中可以直接转成对象，比 SAML 中 XML 强
2. 支持非对称加密，SWT 只支持 HMAC。
3. 支持多种客户端(moblie, pc)


# 最佳实践


## 问题

### 注销和修改密码

>目前没有完美方案，没有官方推荐方案

用户修改密码后，原来的 JWT 还是有效的。

客户端退出清空 JWT 后，原来的 JWT 还是有效的。

方案一：使用 radis 存储 JWT, 实现服务器端销毁 JWT, 但是这样做就和 Session 有点像了

方案二：维护一个 token 黑名单，失效的加入到 token 中

方案三：每个用户设置独立的加密 key，失效就修改 key

### 续签

方案一：每次请求刷新 JWT(肯定不行)

方案二：refreshToken 方案，在返回 token 同时生成一个时间长的 refreshToken 用户刷行 token 时使用; token 过期时 返回指定 code, 提示客户端调用指定接口刷新(JWT)

>微信某个 sdk 中使用了这种方案

## 安全相关

1. 保护签名密钥
2. JWT 不保存敏感数据
3. 防止永生 token
4. 防止 url 缓存攻击

确保不通用户的用户信息包含在 url 中，并在服务器验证, 如users?jwt=xxx–>users?cid=xx&wjt=xxx

5. 日志敏感

在日志中可以存储 JWT 中的数据，但是不直接存储 JWT.

6. 不建议以无签名算法的方式（签名算法为none）来颁发JWT token
7. 更安全的方式，考虑使用非对称加密创建 token
8. Web JWT 安全, toen 存储在 Cookie 是加上 HttpOnly 和 Secure 标记(HttpOnly保证JavaScript无法获取该Cookie，而Secure标记将保证该Cookie信息只能通过Https传输),但注意Cookie的方式可能会引起CSRF（跨站请求伪造）攻击；token可以存放到sessionStorage/localStorage，但注意XSS（跨站点脚本）攻击；
10. Api 防重放机制(接口被用户截获后恶意重复请求)。

方式一：(推荐)

```
可以在claim中 添加 现时标志(jti claim)、过期时间(exp claim)以及创建时间(iat claim)
```

方式二：

```
常用的防止重放的机制是使用 timestamp 和 nonce 来做的重放机制。

timestamp 用来表示请求的当前时间戳

nonce 是由客户端根据足够随机的情况生成的，比如 md5 (timestamp+rand (0, 1000)); 它就有一个要求，正常情况下，在短时间内（比如 60s）连续生成两个相同 nonce 的情况几乎为 0。

服务端
服务端第一次在接收到这个 nonce 的时候做下面行为：
1 去 redis 中查找是否有 key 为 nonce:{nonce} 的 string
2 如果没有，则创建这个 key，把这个 key 失效的时间和验证 timestamp 失效的时间一致，比如是 60s。
3 如果有，说明这个 key 在 60s 内已经被使用了，那么这个请求就可以判断为重放请求。
```

参考：

1. [https://jwt.io/introduction/](https://jwt.io/introduction/)
2. [https://liming.pub/post/jwt/](https://liming.pub/post/jwt/)
3. [http://blog.didispace.com/learn-how-to-use-jwt-xjf/](http://blog.didispace.com/learn-how-to-use-jwt-xjf/)