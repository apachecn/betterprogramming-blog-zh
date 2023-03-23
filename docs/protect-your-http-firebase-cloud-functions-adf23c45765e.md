# 如何保护你的 HTTP Firebase 云功能

> 原文：<https://betterprogramming.pub/protect-your-http-firebase-cloud-functions-adf23c45765e>

## 使用授权持有人

![](img/15fcf4105e6cb4271d2270cd4a207a34.png)

由[安德烈·亨特](https://unsplash.com/@dre0316?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/free?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我每天分享[一招](https://medium.com/@david.dalbusco/one-trick-a-day-d-34-469a0336a07e)直到原定的瑞士新冠肺炎隔离期结束日，2020 年 4 月 19 日。距离第一个里程碑还有 22 天。希望更好的日子就在前面。

去年，我为一个基金会开发了一个应用程序，该基金会的目标是帮助特定年龄的人。主要由于管理原因，该项目尚未公开发布。

最近，我们注意到它的目标和当前的锁定情况有一些相似之处。这就是为什么我被要求创建一个包含有用功能的衍生产品，它可以很快释放出来，因为它会很有用！

我在周六上午“克隆”了我们的 [Firebase Cloud](https://firebase.google.com) 基础设施，并且必须保护我刚刚在线部署的新的 [HTTP 函数](https://cloud.google.com/functions/docs/writing/http)。这就是为什么我有写这篇关于这个主题的简短文章的想法。

# 授权持有人

当您创建一个可以通过 HTTP 请求触发或调用的 Firebase Cloud 函数时，该函数是公共的，可以在 internet 上使用。

据我所知，它生成的 URL 不包含随机 ID 或其他会使路由不可预测的散列，因此，您必须找到一种方法来保护它。

在我看来，这就是为什么解决方案是用授权令牌对每个请求进行身份验证。

解决这个问题的一个方法是在代码中添加一个常量键。例如，如果我们部署了以下功能:

我们可以创建一个函数来验证必须为每个请求提供的`bearer`。

并用它来扩展我们的 HTTP 函数，进行测试。

对于`key`,我们当然可以使用类似于密码或哑关键字的东西，就像我上面使用的那样，但是使用例如版本 4 UUID 会更安全。

有很多工具可以生成它，但是今天，我使用了 [UUID 生成器](https://www.uuidgenerator.net)，它完美地完成了这项工作。

```
const key = '975dd9f6-4a89-4825-9a6d-deae71304a29';
```

因此，我们的 HTTP 路由现在受到保护，只有在提供授权的情况下才可以访问。

```
#!/bin/shcurl -i 
     -H "Accept: application/json"
     -H "Authorization: Bearer 975dd9f6-4a89-4825-9a6d-deae71304a29" 
     -X GET  https://us-central1-yolo.cloudfunctions.net/helloWorld
```

# Firebase 环境变量

保护很酷，但是我们的保护被保护了吗？

如果我们的代码作为开源软件发布，我们的密钥就会暴露。此外，在代码中间处理一个键并不可爱。但是有一个解决方案。

Firebase 提供了定义非公开环境变量的能力，这些变量可以从云函数中读取。

为了这样定义上面的键，我们可以在 Firebase CLI 的帮助下运行下面的命令。

```
#!/bin/sh
firebase functions:config:set hello.world.key="975dd9f6-4a89-4825-9a6d-deae71304a29"
```

剩下要做的事情是用新变量替换我们的常量。

```
const key = functions.config().hello.world.key;
```

也就这样了，我们的 HTTP Firebase 云功能得到了保护。

# 一起

为了以防万一，您可能需要将上面的代码放在一个块中，下面是所有代码:

# 摘要

Firebase 太好用了！但更重要的是:

呆在家里，注意安全！

大卫