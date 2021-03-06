---
title: Cookie浅析之生命周期
date: 2019-01-04 19:40:23
tags: [cookie, session, remember me]
---

### Cookie 的机制

Cookie 是浏览器（UserAgent）访问一些网站后，这些网站存放在客户端的一组数据，用于使网站等跟踪用户，实现用户自定义功能。

Cookie 的 Domain 和 Path 属性标识了这个 Cookie 是哪一个网站发送给浏览器的；Cookie 的 Expires 属性标识了 Cookie 的有效时间，当 Cookie 的有效时间过了之后，这些数据就被自动删除了。

如果不设置过期时间，则表示这个 Cookie 生命周期为浏览器会话期间，只要关闭浏览器窗口，Cookie 就消失了。这种生命期为浏览会话期的 Cookie 被称为会话 Cookie。会话 Cookie 一般不保存在硬盘上而是保存在内存里。如果设置了过期时间，浏览器就会把 Cookie 保存到硬盘上，关闭后再次打开浏览器，这些 Cookie 依然有效直到超过设定的过期时间。存储在硬盘上的 Cookie 可以在不同的浏览器进程间共享，比如两个 IE 窗口。而对于保存在内存的 Cookie，不同的浏览器有不同的处理方式。

### Session 的机制

Session 是存放在服务器端的类似于 HashTable 结构（每一种 Web 开发技术的实现可能不一样，下文直接称之为 HashTable）来存放用户数据，当浏览器第一次发送请求时，服务器自动生成了一个 HashTable 和一个 SessionID 用来唯一标识这个 HashTable，并将其通过响应发送到浏览器。当浏览器第二次发送请求，会将前一次服务器响应中的 SessionID 放在请求中一并发送到服务器上，服务器从请求中提取出 SessionID，并和保存的所有 SessionID 进行对比，找到这个用户对应的 HashTable。

一般情况下，服务器会在一定时间内（默认 20 分钟）保存这个 HashTable，过了时间限制，就会销毁这个 HashTable。在销毁之前，程序员可以将用户的一些数据以 Key 和 Value 的形式暂时存放在这个 HashTable 中。当然，也有使用数据库将这个 HashTable 序列化后保存起来的，这样的好处是没了时间的限制，坏处是随着时间的增加，这个数据库会急速膨胀，特别是访问量增加的时候。一般还是采取前一种方式，以减轻服务器压力。

#### Session 的客户端实现形式（即 SessionID 的保存方法）

一般浏览器提供了两种方式来保存，还有一种是程序员使用 HTML 隐藏域的方式自定义实现：

1. 使用 Cookie 来保存，这是最常见的方法，本文“记住我的登录状态”功能的实现正式基于这种方式的。服务器通过设置 Cookie 的方式将 SessionID 发送到浏览器。如果我们不设置这个过期时间，那么这个 Cookie 将不存放在硬盘上，当浏览器关闭的时候，Cookie 就消失了，这个 SessionID 就丢失了。如果我们设置这个时间为若干天之后，那么这个 Cookie 会保存在客户端硬盘中，即使浏览器关闭，这个值仍然存在，下次访问相应网站时，同样会发送到服务器上。

2. 使用 URL 附加信息的方式，也就是像我们经常看到 JSP 网站会有 aaa.jsp?JSESSIONID=xxx 一样的。这种方式和第一种方式里面不设置 Cookie 过期时间是一样的。

3. 第三种方式是在页面表单里面增加隐藏域，这种方式实际上和第二种方式一样，只不过前者通过 GET 方式发送数据，后者使用 POST 方式发送数据。但是明显后者比较麻烦。

#### 实现“记住我的登录状态”的功能

前面我们了解到，如果我们将 SessionID 通过 Cookie 发送到客户端的时候设置其过期时间为 1 年，那么在今后的一年时间内，客户端访问我的网站的时候都回将这个 SessionID 值发送到服务器上，服务器根据这个 SessionID 从内存或者数据库里面恢复存放 Key-Value 对的 Hashtable。

其实这已经很好的实现了我们的功能了。但是，前面也提到了，实际上 Session 并不会一直都存在的，过了一定的时间之后，服务器上的 Session 就被销毁了，以减轻服务器的访问压力。当服务器上的数据被销毁后，即使客户端上存放了 Cookie 也没有办法“记住我的登录状态”了。通用的实现办法是，将用户的用户名和加密之后的密码也通过 Cookie 的方式存放在客户端，当服务器上的 Session 销毁以后，使用 Cookie 里面存放的用户名和加密之后的密码重新执行一次登录操作，重建 Session，并更新客户端上 Cookie 中存放的的 SessionID，而这个操作是发生在用户请求一个需要身份验证的页面资源的背后，对于用户来讲是透明的，于是就达到了“记住我的登录状态”的目的了。

参考文献

https://blog.csdn.net/zhang15238156629/article/details/70854899
