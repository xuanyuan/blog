---
title: oauth2.0授权码模式
date: 2019-02-25 15:43:31
tags: ['授权', '令牌', oauth2.0]
---

### 场景模拟

作为好学的我们平时最大的业余爱好可能就是学习了（按下不表）。某天，一时兴起，我们要登录国内一新兴网站发表最近大作。

贴心地提供了两种方式：

- 手机号注册社
- 社交账号登录（俗称第三方）

为了节省宝贵时间，本着精简不浪费原则（主要是记不住），我们选择了 github 进行登录。

> 前提：该网站已经在 github 中创建相关应用，并且 github 给了两份接头信物 client_id 和 client_secret。二者缺一不可，同时限定了回调地址。

点击登录，此时网站会给你它持有的信物之一 client_id，用户持有正确的信物和回调地址，方可打开 github 授权界面，

```
// 用户登录 github，协商
GET //github.com/login/oauth/authorize
// 协商凭证
params = {
  client_id: "xxxx",
  redirect_uri: "http://my-website.com"
}
```

此时我们访问的是 github 认证服务器上的授权页面，点击同意该网站的申请的权限列表，此时 github 在回调地址中添加一个同意标记 code,表示人家姑娘答应了，请求回调地址。

```
// 协商成功后带着盖了章的 code
Location: http://my-website.com?code=xxx
```

回到该网站，目前人家是姑娘是答应了，就看你的了，你现在有 code 了，但是还不能让你得逞，谁知道是不是你偷得，冒充别人呢。怎么办呢？咱们不是说好俩信物吗？两个一起拿来验证我才相信

```
// 网站和 github 之间的协商
POST //github.com/login/oauth/access_token
// 协商凭证包括 github 给用户盖的章和 github 发给我的门票
params = {
  code: "xxx",
  client_id: "xxx",
  client_secret: "xxx",
  redirect_uri: "http://my-website.com"
}
```

好小子，果然是你，我一开始就是这么认为的，既然如此，如花是你的了。以后我们就是一家人了，赠你一枚祖传令牌（access_token）

```
// 拿到最后的绿卡
response = {
  access_token: "e72e16c7e42f292c6912e7710c838347ae178b4a"
  scope: "user,gist"
  token_type: "bearer",
  refresh_token: "xxxx"
}
```

以后只要拿着这个可以自由出入，如入无人之境，当然不是所有信息，能获取用户的哪些权限在 response 中也给了明确的说明，scope 为 user 和 gist，也就是只能获取 user 组和 gist 组两个小组的权限， user 组中就包含了用户的名字和邮箱等信息了：

```
// 访问用户数据
GET //api.github.com/user?access_token=e72e16c7e42f292c6912e7710c838347ae178b4a
```

整个 OAuth2 流程在这里也基本完成了，文章中的表述很粗糙，比如 access_token 这个绿卡是有过期时间的， 如果过期了需要使用 refresh_token 重新签证。重点是让读者理解整个流程。

### 参考文章

https://www.xncoding.com/2017/03/29/web/oauth2.html
