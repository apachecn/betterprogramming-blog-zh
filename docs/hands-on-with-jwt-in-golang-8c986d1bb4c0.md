# 在戈朗与 JWT 一起动手

> 原文：<https://betterprogramming.pub/hands-on-with-jwt-in-golang-8c986d1bb4c0>

## 使用 JWT 构建安全的 API

![](img/f034fac4a68619d425dded65aabbf30b.png)

图片来源: [AppsMaven](https://appsmaventech.com/blog/Use-JSON-Web-Token-To-Secure-Your-RESTful-APIs-A-Guide)

在本文中，我们将创建一个简单的 RESTful API 来注册、登录和授权使用 [JWT](https://jwt.io/) 的用户，但是首先让我们看看我们在这里试图解决的问题。

# 问题和解决方案

## 问题是

这里的问题很简单:您有一个由客户端使用的 API(web、移动应用程序、CLI 等。)并且你想保护它，只授权注册用户访问它。

## 解决方案

解决方案是使用 JSON web 令牌(简称 JWT)来登录和授权用户，如下图所示

![](img/d1b250e1d9856b559d56d90b1de4bf7b.png)

图片来源:作者

1.  客户端将通过向 API 服务器发送凭证来让用户登录。
2.  API 服务器将验证用户凭证，签署一个 JWT，并在 HTTP 响应中返回它。
3.  客户端将使用接收到的 JWT 来访问 API 资源。
4.  API 服务器将验证 JWT 并授权用户访问资源。

# 什么是 JWT？

JWT 或 JSON web token 是一种数字签名字符串，用于在各方之间安全地传输信息。这是一个 RFC7519 标准。

JWT 由三部分组成:

`header.payload.signature`

下面是一个 JWT 的例子。

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

## 标题

头是一个 Base64 编码的字符串，它包含令牌类型(本例中为`JWT`)和签名算法(本例中为`HMAC SHA256`，简称为`HS256`)。

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

## 有效载荷

有效负载是包含声明的 Base64 编码字符串。声明是与用户和令牌本身相关的数据集合。示例声明有:`exp`(到期时间)、`iat`(发布时间)、`name`(用户名)和`sub`(主题)。

```
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

你可以[在这里](https://tools.ietf.org/html/rfc7519#section-4)了解更多关于索赔的信息。

## 签名

签名是一个带符号的字符串。对于 HMAC 签名算法，我们使用 Base64 编码的头部、Base64 编码的有效载荷和签名秘密来创建它。

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

# 戈朗的 JWT

现在我们对 JWTs 有了更好的了解，让我们用 Golang 创建我们的小型认证 API。

## 启动 Go 模块

在您的`GOPATH`中创建一个新目录，将其命名为`authapp`，然后启动一个 Go 模块。

```
go mod init
```

## 数据库

为了便于理解，我将使用一个 SQLite 数据库。
要在 Go 中处理 SQL 数据库，我强烈推荐使用像 [GORM](https://gorm.io/index.html) 这样的 ORM，所以让我们把它和 SQLite 驱动一起安装。

```
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite
```

现在创建一个名为 database 的新文件夹，并使用这段代码启动一个全局数据库对象。

现在让我们来测试一下。

![](img/c22de6a4e8bace675365146d4b6fa904.png)

测试通过了，我们的数据库也准备好了。让我们继续讨论用户模型。

## 用户模型

我们例子中的用户模型很简单:它有名字、电子邮件和密码。
用户密码应该在数据库中散列，为了实现这一点，我们使用了令人惊叹的 [bcrypt](https://www.npmjs.com/package/bcrypt) 库。

```
go get "golang.org/x/crypto/bcrypt"
```

这是完整的用户模型，具有在数据库中创建记录、散列和检查密码的功能。

现在我们来测试一下上面所有的逻辑。

![](img/c7a159e248b7a92eb7f4055313048bcb.png)

到目前为止，一切顺利。现在让我们继续签署 JWT。

## 签署和验证 JWT

为了在 Golang 中签署和验证 JWT 令牌，我们将使用 [jwt-go](https://github.com/dgrijalva/jwt-go) 包。

```
go get github.com/dgrijalva/jwt-go
```

现在，让我们创建一个自定义包，我们可以在其中签名和验证令牌。

我简单地称它为`auth`，它有如下的结构和函数:

*   一个`JwtWrapper`结构，用于包装签名密钥、颁发者和以小时为单位的过期时间
*   向我们的令牌添加自定义电子邮件声明的`JwtClaim`结构
*   使用 HS256 生成 24 小时后到期的令牌的`GenerateToken`函数
*   验证令牌并返回声明的`ValidateToken`函数

下面是完整的代码。

测试时间:

![](img/835ba304c6b54f439b8f0ad2334cedb4.png)

太好了！现在我们已经有了用户模型和 JWT 签名/验证逻辑，是时候创建实际的 API 了。

# API

在这一节中，我们将创建三个 RESTful API 端点

*   创建用户
*   `[POST] /api/public/login` = >让用户登录并返回一个 JWT
*   授权用户并返回请求的数据

但是在我们开始之前，我们需要安装伟大的[gin-gonic](https://github.com/gin-gonic/gin)**web 框架。**

```
go get -u github.com/gin-gonic/gin
```

**现在让我们设置我们的主文件来创建一个数据库，启动路由器和服务器。**

**让我们来测试一下。**

**![](img/e5af2486e70a083f9e2be089a9f8b58e.png)**

**我们的 HTTP 路由器准备好了。现在让我们创建注册控制器。**

## **注册**

**注册是一个公共 API 它应该不需要认证。**

**要创建轧花机控制器，创建一个名为`controllers`的包和一个名为`public.go`的文件。**

**请求已发布。我们应该获取有效负载并验证它，将用户插入数据库，然后返回插入的用户。**

## **登录**

**这里的登录很简单。注册用户只需提供电子邮件和用户名，我们将为他们签署一个令牌。**

**让我们测试注册和登录。**

**![](img/e274bfc7b9d3fb8000501203946dcaf7.png)**

**我们已经在这里做了单元测试，但是让我们创建我们的路线并在[邮递员](https://www.postman.com/)中测试实际的东西。**

**将以下代码添加到`main.go`文件中。**

**现在运行服务器。**

```
go run main.go
```

**让我们在《邮差》中测试一下。**

**![](img/e03f850f5ff36985baaa48ce07f72320.png)****![](img/806fb90311b3c9125e33e9e48d5b338a.png)**

**非常好！让我们在 JWT 验证我们的令牌。**

**![](img/dbf2a1321dd8fdf02ed12f3b57d4e011.png)**

**厉害！现在，用户可以创建帐户、登录并接收令牌。**

**让我们创建一个受保护的路由，只有经过身份验证的用户才能访问它。**

## **受保护的资源**

**这里的资源是一个用户配置文件。这里很简单:它将把用户数据返回给客户机。**

**这里有些奇怪。我们没有做任何令牌验证！请注意我们是如何从 gin 的上下文中获得电子邮件的。**

**授权用户和验证令牌应该发生在中间件中，原因很简单:不要重复自己。**

**但是在转移到授权中间件之前，让我们测试一下配置文件控制器。**

**![](img/c801856fb268505a0bea2cf4fb1feb86.png)**

**非常好。现在谈谈中间件。**

## **授权中间件**

**中间件位于客户机和资源之间，所以在我们访问数据库之前，中间件将被调用来验证令牌和授权用户。授权逻辑非常简单:**

*   **检查授权头中是否存在 JWT。**
*   **检查令牌格式。**
*   **验证令牌。**
*   **前往控制器。**

**创建一个名为`middlewares`的包，并添加以下代码。**

**测试时间:**

**![](img/8506c54f5aaa452c3962e0dfa1814cb6.png)**

**不错！我们的授权中间件非常好用。现在让我们在我们的主路由器中添加一个适当的路由，并在 Postman 中自己查看结果。**

**![](img/61b22a50bef894f060008e727670d32a.png)**

**好像起作用了！**

# **概述**

**就这样，伙计们！我希望到目前为止你喜欢这篇文章。**

**我已经在 [GitHub](https://github.com/hamzawix/jwt-auth-go) 上传了代码**

**延伸阅读:[刷新令牌:何时使用它们以及它们如何与 jwt 交互](https://auth0.com/blog/refresh-tokens-what-are-they-and-when-to-use-them/)**