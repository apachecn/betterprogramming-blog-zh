# 无服务器框架中的白名单 IP 地址

> 原文：<https://betterprogramming.pub/whitelist-ip-address-in-serverless-framework-7f5ede2dfc07>

## 通过将 API 限制到您信任的 IP 地址来保护您的 API 免受不良行为者的攻击

![](img/cc357ed71a3cc928ffe2194516d032b3.png)

照片由[切普·尼科利](https://unsplash.com/@nicoli_?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 介绍

对于我们拥有的某些 API 端点，我们常常希望将其锁定，只对某些人开放。本质上，我们希望保护 API 不被恶意攻击者滥用。

保护我们的 API 的一种方法是通过 IP 地址限制。这是我们指定允许调用 API 端点的 IP 地址范围的地方。幸运的是， [AWS API 网关](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)支持这种保护机制，称为*资源策略*。

当通过无服务器框架开发 API 时，我们可以在`serverless.yml`中指定 API 网关配置。从根本上说，CloudFormation 模板里能写的东西，在`serverless.yml`里也能写。

# 你将学到什么

我们将学习如何从`serverless.yml`为我们的 API 网关配置资源策略，以创建 IP 地址限制来保护我们的无服务器 API。

# 将资源策略配置为将 IP 地址列入白名单

在每个无服务器项目中，所有的配置都将放在`serverless.yml`文件中。就我们今天的目的而言，我们将在文件的`provider`部分编写资源策略配置。

让我们看看如何编写资源策略来限制哪些 IP 地址可以调用 API Gateway 上托管的 API。

无服务器. yml

让我们来看一下这里的几个要点。

## 行动

`Action: execute-api:Invoke`是我们希望通过资源策略控制的动作的名称。这表示一个调用 API 网关上的 API 端点的动作。

## 资源

`Resource`是我们放置想要限制的 API 网关资源(或此上下文中的 API 端点)的字段。上面的例子`execute-api:/*/*/*`表明我们的资源策略适用于我们正在配置的 API 网关上的所有端点。

可以只限制对特定端点的访问。在这种情况下，我们为`Resource`字段输入的值应该是这样的:

```
"arn:aws:execute-api:ap-southeast-1:123456789012:qwa2y1c3m4/dev/POST/authentication"
```

这将只对我们的`/authentication`端点应用资源策略。因此，如果有其他 API 端点托管在同一个 API 网关上，它们将不会受到限制。

## 情况

`Condition`是这里的关键字段。它是我们希望应用于 API 网关中的资源的一组规则(或条件)。在我们的例子中，我们希望通过 IP 地址的范围进行限制。所以，这就是为什么我们有我们的`Condition`作为:

```
IpAddress:          
  aws:SourceIp:
    - "100.126.57.115"
    - "108.190.92.210"
```

这表明只有那些源 IP 地址被允许做`execute-api:Invoke`动作。

如果您尝试从这两个 IP 地址之外的任何 IP 地址调用 API，您将得到如下错误:

```
{"Message": "User: anonymous is not authorized to perform: execute-api: Invoke on resource: arn:aws:execute-api:ap-southeast-1:123456789012:qwa2y1c3m4/dev/POST/authentication"}
```

就是这样！干得好，伙计们！

很简单明了，对吧？

# 保护 API 的其他方法

IP 地址限制(或白名单)只是保护 API 的众多方法之一。以下是为 API 添加保护的另外两种方法:

*   在 API 网关上配置使用计划。使用计划允许您设置每秒允许多少个请求，以及在特定时间段内(例如每天、每周或每月)允许多少个请求。
*   将输入验证放在反序列化请求体的类对象中。你可以在这里看到如何在 Python 中做到这一点: [*通过 Python 3 数据类*](https://towardsdatascience.com/protect-your-api-via-input-validation-in-python-3-data-class-edefa5e280df) 中的输入验证来保护你的 API。

# 包裹

当您读到这一节时，您将已经掌握了如何通过只允许白名单中的 IP 地址调用 API 来限制 API 的知识和技能。

如前所述，还有其他方法来保护您的 API。我鼓励您探索其他选项，并选择最符合您需求的选项。

就是这样！下次见。