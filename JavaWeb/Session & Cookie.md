### token、session、cookie三者的区别

##### token

> 令牌，是用户身份的验证方式

- token是被签名的，可以认为一个解码认证通过的token是由系统开发的，其中带的信息是合法有效的
- token可以跨域，而cookie不能跨域

 

##### cookie

> cookie是一个由服务器生成的存储在用户本地终端上的数据，它会随着请求将其内容发送给服务器

- cookie中存放着一个sessionId，发送一次请求时会携带这个ID
- cookie存储在客户端上，session存储在服务器上



##### session

> 每次客户发送请求，服务器端都会检查是否含有sessionId。如果有，则根据sessionId检索出session并处理；如果没有，则创建一个session，并绑定一个不重复的sessionId

- session是因为请求而产生的
- session作为一个容器，可以存放一次浏览器会话过程中的任何对象
- session存储在服务端的而cookie存储在浏览器中
- session是一种http存储记住，目的是为了武装http提供持久机制



### JWT

> JWT 英文全称是 Json Web Token，通常可以称为 Json 令牌。

##### JWT主要作用如下

- 认证：每当用户登录，用户的每次请求中都会携带 JWT，因而可以访问令牌所允许的路由、服务和资源。在**单点登录**这一功能中 JWT 被广泛应用，因为它的开销很小。
- 信息交换：JWT 是保证信息能够安全传输的一种方式。通过公钥、私钥对 JWT 进行签名认证。

##### JWT 的组成格式

- Header：是 JWT 的标头，它通常由两部分组成：令牌的类型（即 JWT）和使用的（签名算法）
- Payload：它是token的第二部分，Payload 中包含一个声明，声明有三种类型（没遇到过具体情况，因此不太能理解三种声明的区别）
  - registered 声明：
  - public 声明：
  - private 声明：
- Signature：意为签证信息，其由三部分组成（没遇到过具体情况，因此不太能理解三种声明的区别）
  - header
  - payload
  - secret







