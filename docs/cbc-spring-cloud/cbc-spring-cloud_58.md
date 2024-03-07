# JWT（Json Web Token）是什么？

> 原文：[`c.biancheng.net/view/5499.html`](http://c.biancheng.net/view/5499.html)

JWT（Json Web Token）是为了在网络应用环境间传递声明而执行的一种基于 Json 的开放标准。JWT 的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源。

比如用在用户登录上时，基本思路就是用户提供用户名和密码给认证服务器，服务器验证用户提交信息的合法性；如果验证成功，会产生并返回一个 Token，用户可以使用这个 Token 访问服务器上受保护的资源。

JWT 由三部分构成，第一部分称为头部（Header），第二部分称为消息体（Payload），第三部分是签名（Signature）。一个 JWT 生成的 Token 格式为：

token = encodeBase64(header) + '.' + encodeBase64(payload) + '.' + encodeBase64(signature)

头部的信息通常由两部分内容组成，令牌的类型和使用的签名算法，比如下面的代码：

{
  "alg": "HS256", 
  "typ": "JWT"
}

消息体中可以携带一些你需要的信息，比如用户 ID。因为你得知道这个 Token 是哪个用户的，比如下面的代码：

{
  "id": "1234567890", 
  "name": "John Doe", 
  "admin": true
}

签名是用来判断消息在传递的路上是否被篡改，从而保证数据的安全性，格式如下：

HMACSHA256( base64UrlEncode(header)  + "." +  base64UrlEncode(payload), secret)

通过这三部分就组成了我们的 Json Web Token。更多介绍可以查看 JWT 官网 [`jwt.io/introduction/`](https://jwt.io/introduction/)。