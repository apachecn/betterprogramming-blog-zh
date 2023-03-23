# 如何在 Java Spring Boot 中实现 JSON Web Token (JWT)

> 原文：<https://betterprogramming.pub/how-to-implement-json-web-token-jwt-in-java-spring-boot-943084ad1592>

## 这一有用功能的简要指南

![](img/f9d43d06db26763e97c56c1fdfd26b7a.png)

图片来自 Pixabay

JSON Web Token 或 JWT 作为一种服务间安全通信的方式而闻名。JWT 有两种形式:JWS 和 JWE。他们之间的区别是，JWS 的有效载荷没有加密，而 JWE 是。

本文将探讨 Java Spring Boot 中 JWT 的实现。如果你想更多地了解 JWT 本身，你可以在这里访问我的另一篇文章。

本文中的代码托管在以下 GitHub 存储库中:[https://github.com/brilianfird/jwt-demo](https://github.com/brilianfird/jwt-demo)。

# 图书馆

对于本文，我们将使用`jose4j`库。`jose4j`是 Java 中流行的 JWT 库之一，具有完整的特性。如果你想看看其他的库(不管是不是针对 Java 的)，jwt.io 整理了一个列表。

```
<dependency>  
    <groupId>org.bitbucket.b_c</groupId>  
    <artifactId>jose4j</artifactId>  
    <version>0.7.12</version>  
</dependency>
```

# 用 Java 实现 JWS

JSON 网络签名(JWS)由三部分组成:

*   何塞·海德
*   有效载荷
*   签名

让我们来看一个何塞标题的例子:

```
{
	alg:"HS264"
}
```

JOSE header 存储了关于如何处理 JWS 的元数据。
`alg`存储关于 JWT 使用的签名算法的信息。

接下来，让我们检查有效载荷:

```
{
  "sub": "1234567890",
  "name": "Brilian Firdaus",
  "iat": 1651422365
}
```

JSON payload 存储我们想要传输给客户机的数据。它还储存了一些 JWT 声称的信息，我们可以核实。

在上例中，我们有三个注册为 JWT 索赔的字段。

*   `sub`表示用户的唯一 id
*   `name`表示用户的姓名
*   `iat`表示我在一个纪元中创造 JWT 的时候

最后一部分是签名，这是使 JWS 安全的部分。通常，JWS 的签名将以字节的形式出现。让我们看一个 Base64 编码签名的例子:

```
qsg3HKPxM96PeeXl-sMrao00yOh1T0yQfZa-BsrtjHI
```

现在，如果我们看到上面的三个部分，您可能会想知道如何将这三个部分无缝地传递给消费者。答案是紧凑序列化。使用紧凑序列化，我们可以轻松地与消费者共享 JWS，因为 JWS 将成为一个长字符串。

```
Base64.encode(JOSE Header) + "." + Base64.encode(Payload) + "." + Base64.encode(signature)
```

结果将是:

```
eyJhbGciOiJIUzI1NiIsImtpZCI6IjIwMjItMDUtMDEifQ.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkJyaWxpYW4gRmlyZGF1cyIsImlhdCI6MTY1MTQyMjM2NX0.qsg3HKPxM96PeeXl-sMrao00yOh1T0yQfZa-BsrtjHI
```

在 JWT 规范中，紧凑序列化部分也是强制性的。因此，要将 JWS 视为 JWT，我们必须进行紧凑的序列化。

# 无保护的

我们将探索的第一种 JWS 是没有保护的 JWS。人们很少使用这种类型的 JWS(基本上只是一个普通的 JSON)，但是让我们先来研究一下，以了解实现的基础。

让我们从创建标题开始。与之前使用`HS256`算法的例子不同，现在我们不使用任何算法。

# **生产无保护的 JWS**

让我们看看我们在代码中做了什么:

*   我们设置了一堆索赔(`sub`、`iat`、`exp`、`iss`、`name`、`email`、`email_verified`)
*   我们将签名算法设置为`NONE`并将算法约束设置为`NO_CONSTRAINT`，因为`jose4j`会因为算法缺乏安全性而抛出异常
*   我们将 JWS 打包在紧凑序列化中，这将产生一个包含 JWS 的字符串。结果是一个 JWT 编译的字符串。

让我们看看调用`jws.getCompactSerialization()`会得到什么输出:

```
eyJhbGciOiJub25lIn0.eyJzdWIiOiI3NTYwNzU1ZS1mNDVkLTRlYmItYTA5OC1iODk3MWMwMmViZWYiLCJpYXQiOjE2NTI1NTYyNjYsImV4cCI6MTY1MzE2MTA2NiwiaXNzIjoiaHR0cHM6Ly9jb2RlY3VyYXRlZC5jb20iLCJuYW1lIjoiQnJpbGlhbiBGaXJkYXVzIiwiZW1haWwiOiJicmlsaWFuZmlyZEBnbWFpbC5jb20iLCJlbWFpbF92ZXJpZmllZCI6dHJ1ZX0.
```

如果我们尝试解码它，我们将得到带有我们之前设置的字段的 JWS:

```
{
  "header": {
    "alg": "none"
  },
  "payload": {
    "sub": "7560755e-f45d-4ebb-a098-b8971c02ebef",
    "iat": 1652556266,
    "exp": 1653161066,
    "iss": "https://codecurated.com",
    "name": "Brilian Firdaus",
    "email": "brilianfird@gmail.com",
    "email_verified": true
  }
}
```

我们已经成功地用 Java 的`jose4j`库创建了一个 JWT！现在，让我们继续 JWT 消费过程。

为了使用 JWT，我们可以使用`jose4j`库中的`JwtConsumer`类。让我们看一个例子:

通过使用`JwtConsumer`，我们可以很容易地制定关于在处理传入的 JWT 时验证什么的规则。它还提供了一种简单的方法，通过分别使用`.getJoseObjects()`和`getJwtClaims()`来获得 JWS 对象和声明。

既然我们知道了如何在没有签名算法的情况下生产和消费 JWT，那么理解有签名算法的情况就容易多了。不同之处在于，我们需要设置算法并创建密钥来生成/验证 JWT。

# HMAC·SHA-256

HMAC SHA-256( `HS256`)是一个带有对称密钥的 MAC 函数。我们将需要为它的密钥生成至少 32 个字节，并将其提供给`jose4j`库中的`HmacKey`类，以确保安全性。

我们将使用 Java 中的`SecureRandom`库来确保密钥的随机性。

```
byte[] key = new byte[32];  

SecureRandom secureRandom = new SecureRandom();  
secureRandom.nextBytes(key);

HmacKey hmacKey = new HmacKey(key);
```

密钥应被视为凭证。因此，它应该存储在一个安全的环境中。作为建议，可以将其存储为环境变量或者存储在[Vault]中(https://www.vaultproject.io/)。

让我们看看如何创建和消费与`HS256`签约的 JWT:

与创建没有签名算法的 JWS 相比，代码没有太大区别。我们首先使用`SecureRandom`和`HmacKey`类制作密钥。因为`HS256`使用对称密钥，所以我们只需要一个密钥来签名和验证 JWT。

我们还通过使用`jws.setAlgorithmheaderValue(AlgorithmIdentifiers.HMAC_SHA256`和带有`jws.setKey(hmacKey)`的密钥将算法头值设置为`HS256`。

在 JWT 消费者中，我们只需要通过使用`jwtConsumer`对象上的`.setVerificationKey(hmacKey)`来设置 HMAC 密钥`jose4j`将通过解析其 JOSE 报头来自动确定 JWS 中使用的算法。

# ES256

与只需要一个密钥的`HS256`不同，我们需要为`ES256`算法生成两个密钥:私钥和公钥。

我们可以使用私钥来创建和验证 JWT，而我们只能使用公钥来验证 JWT。由于这些特点，私钥通常作为凭证存储，而公钥可以作为 JWK 公开托管，因此 JWT 的消费者可以查询主机并自己获得密钥。

`jose4j`库提供了一个简单的 API 来生成私有和公共密钥作为 JWK。

现在我们知道了如何用`ES256`算法生成创建 JWT 的密钥，这与用`HS256`算法创建 JWT 几乎是一样的。

唯一不同的是:

*   我们将算法头设为`ECDSA_USING_P256_CURVE_AND_SHA256`
*   我们在创建 JWT 时使用私钥
*   我们使用公钥来验证 JWT

# 主持 JWK

我们可以使用`JsonWebKeySet`类轻松创建 JSON Web Key Set。

我们还需要更改密钥解析器的一些属性:

因为我们托管了 JSON Web 密钥集，所以我们需要查询主机。`jose4j`也通过使用`HttpsJwksVerificationKeyResolver`提供了一种简单的方法。

# 用 Java 实现 JWE

与 JWS 不同，JSON Web Encryption 是一种 JWT，它被加密，所以除了有私钥的人之外，没有人能看到它的内容。首先，让我们看一个例子:

```
eyJhbGciOiJFQ0RILUVTK0EyNTZLVyIsImVuYyI6IkExMjhDQkMtSFMyNTYiLCJlcGsiOnsia3R5IjoiRUMiLCJ4IjoiMEdxMEFuWUk1RVFxOUVZYjB4dmxjTGxKanV6ckxhSjhUYUdHYzk5MU9sayIsInkiOiJya1Q2cjlqUWhjRU1xaGtubHJ6S0hVemFKMlhWakFpWGpIWGZYZU9aY0hRIiwiY3J2IjoiUC0yNTYifX0.DUrC7Y_ejpt1n9c8wXetwU65sxkEYxG6RBsCUdokVODJBtwypL9VjQ.ydZx-UDWDN7jbGeESXvPHg.6ksHUeeGgGj0txFNXmsSQUCnAv52tJuGR5vgrX54vnLkryPFv2ATdLwYXZz3mAjeDes4s9otz4-Fzg1IBZ4qsfCVa6_3CVdkb8BTU4OvQx23SFEgtj8zh-8ZrqZbpKIT.p-E09mQIleNCCmwX3YL-uQ
```

JWE 的结构是:

```
BASE64URL(UTF8(JWE Protected Header)) || ’.’ ||
BASE64URL(JWE Encrypted Key) || ’.’ ||
BASE64URL(JWE Initialization Vector) || ’.’ ||
BASE64URL(JWE Ciphertext) || ’.’ ||
BASE64URL(JWE Authentication Tag)
```

而如果我们解密 JWE，我们会得到如下的说法:

```
{
	"iss":"https://codecurated.com",
	"exp":1654274573,
	"iat":1654256573,
	"sub":"12345"
}
```

现在，让我们看看如何创建 JWE:

与 JWS 相比，创造和消费 JWE 的主要区别在于:

*   我们使用公钥作为加密密钥，使用私钥作为解密密钥
*   我们在 JWE 没有签名，所以消费者需要跳过签名要求

# 结论

在本文中，我们已经学会了使用`jose4j`在 Java 中创建 JWS 和 JWE。

希望这篇文章对你有用。如果你想了解更多关于 JWT 的概念，你可以访问[我的另一篇文章。](https://codecurated.com/blog/introduction-to-jwt-jws-jwe-jwa-jwk/)