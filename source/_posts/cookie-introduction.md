---
title: Cookie浅析
date: 2019-01-04 19:40:23
tags:
---

### 概述

1. 特点

- Cookie 是服务器保存在浏览器上的一小段文本信息
- 由键值对构成，由分号和空格隔开
- 存储在浏览器，但通常由服务器端设置
- 每个 Cookie 的大小一般不能超过 4KB
- 浏览器每次向服务器发送请求，就会自动附上这段信息。

2. 应用场景：

- 对话管理：保存登录，购物车等
- 记录用户行为和偏好信息

```javascript
// 获取当前网页的Cookie
document.cookie
// 检查浏览器是否打开Cookie功能
window.navigator.cookieEnabled // true
```

### 属性

| 属性     | 含义     | 默认     | 协议     |
| -------- | -------- | -------- | -------- |
| name     | 名字     | &nbsp;   | &nbsp;   |
| value    | 值       | &nbsp;   | &nbsp;   |
| expires  | 到期时间 | &nbsp;   | http/1.0 |
| max-age  | 到期时间 | &nbsp;   | http/1.1 |
| domain   | 所属域名 | 当前域名 | &nbsp;   |
| path     | 所属域名 | 当前网址 | &nbsp;   |
| secure   | 安全     | 空       | &nbsp;   |
| httpOnly | 只读     | 空       | &nbsp;   |

#### name=value

Cookie 就是一段字符串。但这个字符串是有格式的，由键值对 key=value 构成，键值对之间由一个分号和一个空格隔开。
第一个键值对是 Cookie 真正的值，后面的为 Cookie 属性

#### expires/max-age

expires 是 http/1.0 协议中的选项，在新的 http/1.1 协议中 expires 已经由 max-age 选项代替，两者的作用都是限制 Cookie 的有效时间。expires 的值是一个时间点 (Cookie 失效时刻 = expires)，而 max-age 的值是一个以秒为单位时间段 (Cookie 失效时刻 = 创建时刻 + max-age)
如果 Set-Cookie 字段没有指定 Expires 或 Max-Age 属性，那么这个 Cookie 就是 Session Cookie，即它只在本次对话存在，一旦用户关闭浏览器，浏览器就不会再保留这个 Cookie。

> 如果同时设置了 max-age 和 expires，以 max-age 的时间为准。

#### domain/path

name、domain 和 path 可以标识一个唯一的 Cookie。domain 和 path 两个选项共同决定了 Cookie 何时被浏览器自动添加到请求头部中发送出去。具体是什么原理请看 Cookie 的作用域和作用路径 这个章节。

如果没有设置这两个选项，则会使用默认值。domain 的默认值为设置该 Cookie 的网页所在的域名，path 默认值为设置该 Cookie 的网页所在的目录。

> 在子路径内可以访问访问到父路径的 Cookie，反过来就不行。

#### secure

secure 选项用来设置 Cookie 只在确保安全的请求中才会发送。当请求是 HTTPS 或者其他安全协议时，包含 secure 选项的 Cookie 才能被保存到浏览器或者发送至服务器。

默认情况下，Cookie 不会带 secure 选项(即为空)。所以默认情况下，不管是 HTTPS 协议还是 HTTP 协议的请求，Cookie 都会被发送至服务端。

#### httpOnly

这个选项用来设置 Cookie 是否能通过 js 去访问。默认情况下，Cookie 不会带 httpOnly 选项(即为空)，客户端是可以通过 js 代码去访问（包括读取、修改、删除等）这个 Cookie 的。当 Cookie 带 httpOnly 选项时，客户端则无法通过 js 代码去访问（包括读取、修改、删除等）这个 Cookie。

### 参考文章

http://javascript.ruanyifeng.com/bom/cookie.html

(上面阮一峰大师的大作，不过个人更偏爱下面这边文章)
https://segmentfault.com/a/1190000007243675
