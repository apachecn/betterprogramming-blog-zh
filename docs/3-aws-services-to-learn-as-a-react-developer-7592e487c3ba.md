# 作为 React 开发人员要学习的 3 个 AWS 服务

> 原文：<https://betterprogramming.pub/3-aws-services-to-learn-as-a-react-developer-7592e487c3ba>

## 对云的了解会让你在就业市场上更有价值

![](img/debd12be405dacb9e0d7f0bfc2870e31.png)

波格丹一世·库普里茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

云是未来。越来越多的公司将他们的基础设施迁移到云上，因此对熟悉云技术的开发人员的需求很大！

AWS 是云市场上最大的玩家。因此，从 AWS 开始是完全有意义的。

但是你从哪里开始呢？AWS 有超过 170 项服务。

今天，我将告诉你与 React 开发者最相关的三个服务。

# 主要成分

任何现代应用程序都有一些基本特征:

*   储存；储备
*   数据库ˌ资料库
*   证明
*   主办；主持

在本文中，我们将看到哪些 AWS 服务可以帮助我们实现这些特性。作为一名 React 开发人员，他们应该是学习 AWS 的首要任务。

# 1.S3(简单存储服务)

根据[的文档](https://aws.amazon.com/s3/):

> 亚马逊简单存储服务(亚马逊 S3)是一种对象存储服务，可提供行业领先的可扩展性、数据可用性、安全性和性能

对于前端开发人员来说，S3 的知识是绝对必要的，因为现在许多公司都在利用这种便宜且易于使用的服务！

AWS S3 有两个主要的使用案例。

## 储存；储备

说你在搭建一个电商平台。你需要存储大量的图像。你把它们存放在哪里？

或者，您可能有一个小应用程序，其中您处理非常大的 JSON 数据集，这些数据集不适合存储在任何数据库中。

AWS S3 非常适合这些使用案例。一些主要优势:

*   比较便宜的
*   安全的
*   高度可用

以下是可供您开始使用的资源:

[](https://javascript.plainenglish.io/how-to-upload-files-to-aws-s3-in-react-591e533d615e) [## 如何在 React 中将文件上传到 AWS S3

### 以两种不同的方式

javascript.plainenglish.io](https://javascript.plainenglish.io/how-to-upload-files-to-aws-s3-in-react-591e533d615e) 

## 主办；主持

任何单页应用程序(SPA)在构建后都会生成一些静态文件。这意味着你可以通过访问这些文件来访问网站。

这就是为什么 AWS S3 是您的绝佳选择。您只需将文件上传到 bucket 并访问 URL。就这么简单。它还有其他一些优点:

*   HTTPS 默认
*   易于与 Route53 集成
*   轻松支持 CI/CD 管道
*   连接 CDN 很简单

这些就是你应该把你的应用程序放在 S3 的原因。以下是帮助您入门的资源:

[](https://javascript.plainenglish.io/continuous-deployment-pipeline-for-react-apps-886f887996f8) [## React 应用的持续部署渠道

### 使用 Github 操作和 AWS S3

javascript.plainenglish.io](https://javascript.plainenglish.io/continuous-deployment-pipeline-for-react-apps-886f887996f8) 

这就是为什么我认为 AWS S3 是 React 开发人员最应该学习的服务！

# 2.DynamoDB

根据[文档](https://aws.amazon.com/dynamodb/):

> “Amazon DynamoDB 是一个键值和文档数据库，在任何规模下都能提供一位数的毫秒级性能。”

它在公司中非常受欢迎，原因如下:

*   快速且易于使用
*   灵活的数据结构
*   高度可扩展
*   可预测的性能

因此，如果你是一个纯粹的前端开发人员，但想使用某种数据库，那么 DynamoDB 是你最好的选择。

很有可能在将来的某个时候，您将需要使用 DynamoDB 进行一些应用，所以让我们现在就开始学习吧！

[](/how-to-use-aws-dynamodb-in-react-70b55ffff93e) [## 如何在 React 中使用 AWS DynamoDB

### 让我们尝试从 React 应用程序访问 DynamoDB

better 编程. pub](/how-to-use-aws-dynamodb-in-react-70b55ffff93e) 

# 3.增强

无论您正在构建什么样的应用程序，在某个时候，您都需要向它引入身份验证。如果你是前端开发者，那么自己设置一个合适的认证后端是非常痛苦的。

这就是 Firebase 等服务的用武之地。虽然我相信 Firebase 更好，但如果您或您的公司只想使用 AWS 服务，那么 [AWS Amplify](https://aws.amazon.com/amplify/authentication/) 就是您的解决方案！

它消除了设置认证机制的麻烦。此外，它对 React 的支持非常好，所以使用它很容易。

[](https://javascript.plainenglish.io/how-to-setup-aws-authentication-for-react-applications-8bbf03bb722) [## 如何为 React 应用程序设置 AWS 身份验证

### 简单但强大

javascript.plainenglish.io](https://javascript.plainenglish.io/how-to-setup-aws-authentication-for-react-applications-8bbf03bb722) 

# 结论

就受欢迎程度和与 React 的集成而言，这是三个最相关的 AWS 服务。对反应良好的显影剂的需求非常高。但是如果你对云有所了解，那么这可以让你成为任何公司更有吸引力的雇员！

你还在等什么？

**有什么话要说？通过** [**LinkedIn**](https://www.linkedin.com/in/56faisal/) 与我联系