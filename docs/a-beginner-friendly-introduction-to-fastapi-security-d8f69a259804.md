# 对初学者友好的 FastAPI 安全性介绍

> 原文：<https://betterprogramming.pub/a-beginner-friendly-introduction-to-fastapi-security-d8f69a259804>

## FastAPI 中的基本认证

![](img/97ebcea1015ee525107fd46ce68493f5.png)

照片由[亚历山大·埃伦霍弗](https://unsplash.com/@alexeh99?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

当你创建一个 API 时，安全性是非常重要的。假设攻击者找到了进入您的 API 的方法，那么在最坏的情况下，他可以修改您的数据。当然，你想一直避免这种情况。因此，最好的第一步是添加一种简单的身份验证方式。

有了用户名和密码，攻击者就很难访问您的 web 应用程序。本文将教你如何用 FastAPI 实现基本的 auth。

# 从控制器访问凭证

如果您想要保护 rest 控制器，您必须定义一个安全方案。这已经在 FastAPI 中提供了，节省了您的时间和精力。没有比这更复杂的了:

```
http_basic = fastapi.security.HTTPBasic()
```

接下来，您将需要查阅用户的数据。API 用户在基本认证中指定用户名和密码。这些都在一个对象中，即`credentials`。下面的代码显示了如何查询凭据:

```
@app.get("/books")
def find_all_books(credentials: HTTPBasicCredentials = fastapi.Depends(http_basic)) -> List[Book]:
    security_service.verify(credentials)
    return book_service.find_all()
```

下面是一个查找图书列表的代码示例。用户提供的凭证可通过参数`credentials`访问。

`security service`将核实这些凭证。如果这些是错误的，这段代码将抛出 401 错误:未授权。安全服务验证功能是一种保护方法。否则，执行流程继续并返回一个图书列表。

# 防止向攻击者泄露信息

基本身份验证是保护 API 的一种简单方法。实现错误使其易受攻击。攻击者知道算法是如何工作的。他们利用有针对性的攻击来获取信息。

您必须防御的最重要的攻击是暴力攻击和定时攻击。

防止随机选择用户名和密码的暴力攻击的最佳方法是选择由不同字符、数字和符号组成的长用户名和密码。这将使窃取您的密码变得非常困难。

> 永远不要在源代码中存储凭据。将它们存储在环境变量中，或者使用 Azure Vault 或 AWS KMS 等专用服务。

攻击者还会试图获得至少一项权利。如果他能得到用户名或密码，他的工作就会减半。因此，永远不要告诉用户密码或用户名是否有效。要么都有，要么都没有。此外，在进行比较之前，我将用户名和密码连接起来。这样，它们被视为一个凭证。

最后，我们使用`secure`模块的`compare_digest`函数来防止计时攻击，这将向攻击者指示密码的长度。结果如下所示:

# 结论

在本文中，您已经了解到在 FastAPI 中添加基本身份验证很容易。您可以阅读凭据并开始检查它们。如果用户没有提供正确的凭证，您将返回 401。否则，流程继续。

然而，您应该仔细考虑如何实现基本身份验证。通过简单的实现错误，您可以使自己非常容易受到攻击者的攻击。所以，如果你想更好地保护自己，一定要采取额外的措施。

合理的下一步是身份验证方法，在这种方法中，您必须使用第三方来识别您的身份，如您的身份证或面部识别。这种额外的安全措施将使攻击者极难获得对您的 API 的访问权限。

然而，对于风险较低的应用程序，依靠基本的身份验证并定期对云中受到良好保护的系统进行良好的备份就足够了。