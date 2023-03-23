# AWS SAM —使用 Lambda 和 DynamoDB 设置本地无服务器开发

> 原文：<https://betterprogramming.pub/aws-sam-setting-local-serverless-development-with-lambda-and-dynamodb-5b4c7375f813>

## AWS SAM 及其流程概述

![](img/303d35966c1cf096ae9e1e3b6b6a48d4.png)

照片由[凯特埃利斯](https://unsplash.com/@caitellis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 介绍

[AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) 代表无服务器应用模型。这是一个专门针对 AWS 资源的无服务器框架。它用于定义、测试和部署无服务器应用程序。SAM 模板可能类似于 CloudFormation——这是理所当然的，因为 cloud formation 位于其下，负责部署部分。

当您部署我们完全定义的堆栈(lambda 函数、角色、S3 桶、无服务器本机数据库)时，您可以在非生产阶段在云环境中进行测试(可能会产生费用)。通过在本地运行一切，开发过程可能会更快、更便宜——在本文中您将构建这样一个设置。

# 准备

## 要求

*   [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)
*   [AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-getting-started.html)
*   [码头工人](https://docs.docker.com/get-docker/)

## 初始化项目

你可以浏览所有模板或者使用`sam init`从头开始写，但是我们将从 AWS 提供的“无服务器 API”模板开始。

```
sam init --name my-sam-app --runtime nodejs14.x --app-template quick-start-web
```

## 概观

现在，让我们快速看一下描述设置的`template.yaml`。你的目标是在 20 秒内理解资源。好了，开始！

用于文章目的的可读版本。在工作目录中找到完整的文件。

几个 CRUD 函数和一个 DynamoDB 表。在这个框架中不需要定义 API 网关资源和角色，比 Terraform 等价要简单得多。

## 尝试在本地调用事件

您可以使用模拟事件来检查函数是否正常工作。

让我们从模板中选择`getAllItemsFunction`函数，并从`events/`中选择一个相关事件。它应该返回一个空数组，因为还没有插入任何项。

```
sam local invoke getAllItemsFunction -e events/event-get-all-items.json
```

不幸的是，我们得到:

```
Invoke Error  {"errorType":"ResourceNotFoundException","errorMessage":"Requested resource not found"...
```

这是因为我们本地没有 DynamoDB。

看看从函数处理程序`src/handlers/get-all-items.js`中提取的这几行:

AWS SDK(在第一行中导入)依赖于执行上下文——并在部署到云中时神奇地找到相关的 DynamoDB 服务。在当地，它什么也没找到。

# 解决办法

## 本地添加 DynamoDB

使用官方 AWS 映像在 Docker 中运行 DynamoDB。命令如下:

```
docker run -p 8000:8000 amazon/dynamodb-local
```

## 调整函数处理程序

更改`src/handlers/get-all-items.js`第一行的`docClient`声明

它将根据 AWS 的 SAM 环境改变所使用的连接，然后指向本地 DynamoDB。

我们使用`host.docker.internal`而不是`localhost`，因为 AWS SAM 事件启动器已经在 docker 容器中。这样，我们就可以从客户容器中获得主机的地址。我知道，很惊讶。

## 在本地创建一个表

我们使用的是`SimpleTable`资源，它代表 DynamoDB 中的一个表。它是自动创建或使用的(如果存在)，但我们必须在本地创建它。

使用表名`SampleTable`，以便本地表自动符合`template.yaml`和现有的处理程序。

对属性和键做同样的事情——使用在`template.yaml`中定义的`id`

```
aws dynamodb create-table --table-name SampleTable --attribute-definitions AttributeName=id,AttributeType=S --key-schema AttributeName=id,KeyType=HASH --billing-mode PAY_PER_REQUEST --endpoint-url http://localhost:8000
```

检查它是否是由以下内容创建的:

```
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

## 再次运行测试事件

```
$ sam local invoke getAllItemsFunction -e events/event-get-all-items.json[...]{"statusCode":200,"body":"[]"}
```

请求已处理。身体确实是一个空数组。你做到了！🏆

现在，您可以在连接数据库的情况下继续开发您的 lambda 函数。如果准备上线，可以用`sam deploy`来做。

# 摘要

在本文中，您有:

1.  了解 AWS SAM 基础知识
2.  增加了本地 DynamoDB 集成
3.  为您漂亮的应用程序准备好本地开发设置

在某些场景中，在无服务器环境中构建完全集成的本地开发环境需要花费太多的精力。必须对代码库进行更改，根据变量是在本地执行还是远程执行来切换变量。

这可能会导致不一致，进而导致代码在本地运行良好，但在云中却无法运行。考虑您是否需要本地的整体集成，或者 CI/CD 是否应该负责集成测试。

最重要的是，DynamoDB 具有平滑的本地集成，因为 AWS 提供了 docker 映像。使用其他更不常见的资源进行设置可能会很麻烦。

[](/build-a-discord-bot-with-aws-lambda-api-gateway-cc1cff750292) [## 用 AWS Lambda + API 网关构建一个 Discord Bot

### 赶上无服务器时代

better 编程. pub](/build-a-discord-bot-with-aws-lambda-api-gateway-cc1cff750292) 

您正在使用什么样的无服务器框架，它如何简化您的开发？

请在下面的评论中告诉我！