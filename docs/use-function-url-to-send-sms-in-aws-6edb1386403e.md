# 如何在 AWS 中使用 AWS Lambda 函数 URL 发送短信

> 原文：<https://betterprogramming.pub/use-function-url-to-send-sms-in-aws-6edb1386403e>

## AWS Lambda 新功能的使用案例

![](img/cdf912e3c77173aac6b5fbb0cdda1f0e.png)

保罗·花冈在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

大家好。今天我们来谈谈 Lambda 中新的函数 URL 特性。我在为一个客户做的一个小项目中使用了这个新功能，这个项目涉及到在 AWS 中使用自定义 OTP 发送 SMS。

# 什么是函数 URL？

函数 URL 是 AWS Lambda 的一项新功能，它允许 HTTPS URL 调用 Lambda 函数。当您创建一个函数 URL 时，Lambda 会生成一个唯一的 URL 端点，如下所示:

```
https://<url-id>.lambda-url.<region>.on.aws
```

## Lambda 函数 URL 和 API 网关有什么区别？

如果不需要 API Gateway 的高级特性，比如缓存、stage、throttling 或 auth，而只需要一个端点来调用 lambda，那么可以使用第一个。

您将无法管理状态代码，但可以为函数 URL 设置 CORS。

*每次你调用一个函数 URL，都会有一次 Lambda 的调用。*

出于这个原因，函数 URL 对于 webhook 是很好的，在这种服务中，您不需要缓存需要立即执行操作(如发送 SMS)的服务的结果。

对于这个项目，我使用 SNS 来发送带有 OTP 消息的 SMS。

# 什么是 SNS？

*亚马逊简单通知服务(Amazon SNS)是一项托管服务，提供从发布者到订阅者的消息传递。*

SNS 有许多发送通知的渠道，如电子邮件、推送通知、短信等。这项服务非常便宜(每百万条请求 0.50 美元)，但短信服务有额外的费用，而且每个国家都不一样。

## 如何退出沙盒模式

短信服务有沙盒模式，所以你只能发送短信给验证过的号码。如果您想退出，您必须从 AWS 控制台打开一个支持案例。

为此，您需要一些信息，如“通信类型”、“国家”、“当月消费限额”等。提交几个小时后，你就可以到处发短信了。

# 一个小例子

在 SAM 模板中，我启用了 Lambda 函数中的函数 URL:

```
SendSmsFunction:
  Type: AWS::Serverless::Function
    Properties:
    CodeUri: ./src
    Runtime: nodejs14.x
    Handler: index.handler
    Architectures:
      — arm64
    FunctionUrlConfig:
      AuthType: NONE
    Policies:
      Statement:
        — Sid: Sns
          Effect: Allow
          Action:
            — sns:Publish
          Resource:
            — “*”
```

以下是使用 AWS SNS 发送短信的代码:

今天到此为止。

感谢您的阅读。

```
**Want to Connect?**Don’t forget to check out the [other articles](https://blog.salvatorecozzubo.com/upload-a-file-in-aws-s3-in-node-js-aede803ac960) on my website.
```