# 测试 AWS SAM 后端基础设施的提示和技巧

> 原文：<https://betterprogramming.pub/tips-and-tricks-for-testing-aws-sam-backend-infrastructure-8a0071f207ef>

## AWS 无服务器应用程序模型

![](img/3536d8fab18eb80e79b40f78ad8b1da2.png)

图片来自[亚马逊](https://aws.amazon.com/lambda/getting-started/)

AWS SAM 是一个工具集，用于将基础设施编写为代码，并将其部署到云中。

该指南将帮助您按照行业最佳实践为您的 [AWS SAM](https://aws.amazon.com/serverless/sam/) 无服务器基础设施构建健壮的集成测试。

# 使用自定义 Lambda 事件进行测试

AWS Lambda 函数可以用隔离函数行为的自定义事件来测试。SAM CLI 为此提供了一个内置工具，以下是使用方法:

```
$ sam local invoke ExampleLambdaFunction -e ../lambda/events/example-event.json
```

示例-event.json

事件主体必须是字符串化的 JSON。您可以尝试 nodeJS `JSON.stringify()`来格式化测试事件体。

如果您进行了更改，请在调用事件之前构建 Lambda 函数:

```
$ sam build
```

# 使用 SAM Local 进行测试

SAM CLI 支持运行 API 网关和 Dockerized Lambda 函数，后者支持使用 HTTP 客户端探测您的 API 网关。这在配置 CORS 或在本地运行前端时非常有用，不会增加 API 调用和 Lambda 执行，这会增加 AWS 账单。

下面是我用来在本地运行 SAM 的脚本:

run-sam-stack-local.sh

关于 SAM CLI 选项，请参见此处的文档。

# 集成测试

集成测试是健壮的后端开发周期的关键部分。

集成测试有助于:

*   功能测试
*   测试驱动开发
*   连续部署——集成测试可以在合并变更或部署之前在部署管道中运行
*   测试 API、Lambda 和 DBs 之间的通信层
*   回归测试——有了足够的功能覆盖，集成测试增加了变更没有意外副作用的信心

以下提示将有助于设置集成测试。

## 在[本地堆栈](https://localstack.cloud/)上运行数据库

LocalStack 是一个模仿 AWS 服务的工具，比如 DynamoDB、S3、SQS 等等。

有几种方法可以运行 LocalStack (Docker、Docker Compose、CLI ),但我更喜欢 Docker Compose，因为它有很多配置选项。

以下配置加速了 DynamoDB 和 S3:

docker-compose.yml

请注意`networks`配置。这使得 Lambda Docker 容器能够与 LocalStack 容器连接。

您会注意到网络是在`run-sam-stack-local.sh`中创建的。该脚本还使用`docker-network`选项告诉 AWS SAM 有关网络的信息。

## 使用现有的测试框架，如 [Jest](https://jestjs.io/)

Jest 是一个令人愉快的 Javascript 测试框架，它可以被配置为在任何测试被触发之前运行一个全局设置脚本。全局设置可以在后台启动 LocalStack:

global-set-js

我不建议在全局 teardown 脚本中停止 LocalStack，因为这会降低测试速度。相反，将其留在后台，以便在开发过程中进行连续测试。

要启用`global-setup.js`，将其包含在 Jest 配置中，如下所示:

jest.config.js

你可能也想在全局设置中触发`run-sam-stack-local.sh`。

这是可能的，但是我更喜欢在一个单独的终端中运行它，在那里你可以在调试时查看 Lambda 函数的输出。

## 在每个测试套件之前填充数据库

要运行集成测试，需要创建并填充数据库。

我建议在每个测试组之前填充数据库，并在测试组完成之后清除它。

这确保了每次运行一个新的测试组时都有一个干净的记录。

Jest 有一个很好的机制来实现这一点，`beforeAll`和`afterAll`方法可以和测试组一起实现:

示例.测试. js

# 资源

*   [https://aws.amazon.com/serverless/sam/](https://aws.amazon.com/serverless/sam/)
*   [https://aws.amazon.com/api-gateway/](https://aws.amazon.com/api-gateway/)
*   [https://aws.amazon.com/lambda/](https://aws.amazon.com/lambda/)
*   [https://www.postman.com/](https://www.postman.com/)
*   [https://marketplace.visualstudio.com/items?itemName=humao.rest-clien](https://marketplace.visualstudio.com/items?itemName=humao.rest-clien)
*   [https://jestjs.io/](https://jestjs.io/)
*   [https://localstack.cloud/](https://localstack.cloud/)
*   [https://github . com/test JavaScript/nodejs-integration-tests-best-practices](https://github.com/testjavascript/nodejs-integration-tests-best-practices)

```
**Want to Connect?**I hope you’ve found this article helpful! [Subscribe to my newsletter](https://tony-oreglia.medium.com/subscribe) or connect on [Twitter](https://twitter.com/tony_oreglia) or [LinkedIn](https://www.linkedin.com/in/tony-oreglia/).
```