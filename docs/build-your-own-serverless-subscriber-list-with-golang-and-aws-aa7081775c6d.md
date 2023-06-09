# 使用 Golang 和 AWS 构建您自己的无服务器用户列表

> 原文：<https://betterprogramming.pub/build-your-own-serverless-subscriber-list-with-golang-and-aws-aa7081775c6d>

## 如何使用 DynamoDB 和 SES 电子邮件注册确认来创建自己的简讯列表

![](img/459dfbd7377a01ed552c262337feabf8.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Rinck 内容工作室](https://unsplash.com/@rinckad?utm_source=medium&utm_medium=referral)拍摄的照片。

以下是我如何充满爱意地建立了一个带有电子邮件确认的订阅注册流程，这个流程很不错。你也可以。

# 简单订阅简介

如果您对管理自己的邮件列表或时事通讯感兴趣，您可以在自己的 AWS 资源上设置简单订阅来收集电子邮件地址。这个开源 API 是用 Go 写的，运行在 AWS Lambda 上。您站点的访问者可以注册您的列表，该列表存储在 DynamoDB 表中，随时可以查询或导出。

当有人注册时，他们会收到一封电子邮件，要求他们确认他们的订阅。这有时被称为“双重选择加入”，尽管我更喜欢“验证”这个术语。简单订阅在无服务器基础设施上工作，并使用 AWS Lambda 来处理订阅、确认和取消订阅请求。

你可以在 GitHub 上找到简单的 SubScribe 项目，以及它完全开放的源代码[。我鼓励你打开代码并跟着做！在本文中，我将分享每个构建步骤、API 单一责任函数背后的思考过程，以及像这样的 AWS 项目的安全性考虑。](https://github.com/victoriadrake/simple-subscribe)

# 构建经过验证的订阅流

未经验证的电子邮件注册过程很简单。有人把他们的电子邮件放进你网站上的一个盒子里，然后那封电子邮件进入你的数据库。然而，如果我已经教过你关于[不信任用户输入](https://victoria.dev/blog/sql-injection-and-xss-what-white-hat-hackers-know-about-trusting-user-input/)的任何事情，一个未经验证的注册过程的想法应该会激怒你。当垃圾邮件被油炸在三明治里时可能会很棒，但当它跑到你的 AWS 账单上时就不好玩了。

虽然你可以使用验证码或谜题这样的策略来验证它是不是人类，但这可能会产生足够的摩擦来拒绝你的潜在订户。相反，确认邮件有助于确保地址的正确性和用户的敏感性。

要构建带有电子邮件确认的订阅流，请创建满足每个逻辑步骤的单一责任函数。这些是:

1.  接受一个电子邮件地址并记录下来。
2.  生成与该电子邮件地址相关联令牌并记录它。
3.  向该电子邮件地址发送带有令牌的确认电子邮件。
4.  接受包含电子邮件地址和令牌的验证请求。

为了实现这些目标，Simple Subscribe 使用官方 AWS SDK for Go 与 DynamoDB 和 ses 进行交互。

在每个阶段，考虑数据是什么样的，以及如何存储。这有助于解决诸如“如果有人尝试订阅两次会怎么样？”或者甚至是[威胁建模](https://victoria.dev/blog/if-you-want-to-build-a-treehouse-start-at-the-bottom/)比如“如果有人用不属于自己的电子邮件订阅怎么办？”

准备好了吗？让我们分解每个步骤，看看魔法是如何发生的。

# 订阅

订阅过程从一个不起眼的 web 表单开始。由于有了浏览器，带有属性`type="email" required`的表单输入有助于验证[。提交时，表单向简单订阅端点发送 GET 请求。](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/email#Validation)

简单订阅接收对该端点的 GET 请求，该请求带有一个查询字符串，其中包含预定订阅者的电子邮件。然后它生成一个`id`值，并将`email`和`id`添加到 DynamoDB 表中。

表格项目现在看起来像这样:

*   电子邮件:`subscriber@example.com`
*   确认:*假*
*   id: `uuid-xxxxx`
*   时间戳:2020 年 11 月 1 日 00 时 27 分 39 秒

`confirm`列包含一个布尔值，表明该项目是一个尚未确认的订阅请求。要验证数据库中的电子邮件地址，您需要找到正确的项目并将`confirm`更改为`true`。

在处理数据时，考虑每个操作的目标，以及如何将传入的请求与现有数据进行比较。

例如，如果有人对同一个电子邮件地址发出后续订阅请求，您会如何处理？您可能会说，“用新的`id`创建一个新的行项目。”然而，当您的无服务器应用程序数据库是按请求量付费时，这可能不是最佳策略。

由于 [DynamoDB 的定价](https://aws.amazon.com/dynamodb/pricing/)取决于您读写表中的数据量，因此避免堆积过多的数据是有利的。

考虑到这一点，谨慎的做法是通过执行更新而不是添加新行来处理同一电子邮件的订阅请求。简单订阅实际上使用相同的函数来添加或更新数据库项。这通常被称为“更新或插入”

在 SQLite 这样的数据库中，这是通过 [UPSERT 语法](https://www.sqlite.org/lang_UPSERT.html)完成的。在 DynamoDB 的情况下，使用更新操作。对于 [Go SDK](https://docs.aws.amazon.com/sdk-for-go/api/service/dynamodb/) ，其语法为`UpdateItem`。

当收到一个重复的订阅请求时，数据库项只在`email`上匹配。如果找到一个现有的行项目，它的`id`和`timestamp`将被覆盖，这将更新现有的数据库记录，并避免重复请求淹没您的表。

# 验证电子邮件地址

提交表格后，预定用户会收到来自 SES 的包含链接的电子邮件。该链接使用表格中的`email`和`id`构建，并采用以下格式:

```
<BASE_URL><VERIFY_PATH>/?email=subscriber@example.com&id=uuid-xxxxx
```

在这个设置中，`id`是一个 UUID，作为一个秘密令牌。它提供了一个可以匹配的标识符，这个标识符足够复杂，很难猜测。这种方法阻止人们用他们不控制的电子邮件地址订阅。

访问链接向您的验证端点发送一个请求，查询字符串中带有`email`和`id`。这一次，将传入的`email`和`id`值与数据库记录进行比较非常重要。这验证了确认电子邮件的收件人正在发起请求。

验证端点确保这些值与数据库中的一个项目相匹配，然后执行另一个更新操作将`confirm`设置为`true`，并更新时间戳。该项目现在看起来像这样:

*   电子邮件:`subscriber@example.com`
*   确认:*真*
*   id: `uuid-xxxxx`
*   时间戳:2020-11-01 00:37:39

# 查询电子邮件

您现在可以查询您的表格来建立您的电子邮件列表。根据您的电子邮件发送解决方案，您可以使用另一个 Lambda 手动完成，甚至可以从命令行完成。

由于请求订阅的数据(其中`confirm`是`false`)与确认的订阅一起存储在表中，因此在查询要发送到的电子邮件地址时区分这些数据非常重要。你会想确保你只回那些`confirm`在`true`的邮件。

# 提供取消订阅链接

与验证电子邮件地址类似，简单订阅使用`email`和`id`作为函数的参数，从 DynamoDB 表中删除一个条目，以便取消订阅电子邮件地址。要允许人们从您的列表中删除自己，您需要在您发送的每封电子邮件中提供一个 URL，其中包括他们的`email`和`id`，作为到取消订阅端点的查询字符串。它看起来会像这样:

```
<BASE_URL><UNSUBSCRIBE_PATH>/?email=subscriber@example.com&id=uuid-xxxxx
```

当单击链接时，查询字符串被传递给取消订阅端点。如果提供的`email`和`id`匹配一个数据库项目，该项目将被删除。

为您的订户提供一种无需任何人工干预即可自动从您的列表中删除的方法，这是处理委托给您的数据的道德和尊重理念的一部分。

# 保护您的数据

一旦你决定接受别人的数据，你就有责任去关心它。这适用于你构建的一切。对于简单的订阅，这意味着维护数据库的安全性并定期修剪表。

为了避免保留超过某个时间范围的`confirm`为`false`的电子邮件地址，设置一个定期运行的清理功能将是一个好主意。这可以通过 AWS Lambda 函数手动实现，也可以通过命令行实现。

要清理，找到数据库项目，其中`confirm`是`false`并且`timestamp`比特定时间点更老。根据您的使用情形和请求量，您选择清理的频率会有所不同。

此外，根据您的使用情况，您可能希望保留数据的备份。如果您特别关注数据完整性，您可以探索 DynamoDB 的[按需备份](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/backuprestore_HowItWorks.html)或[时间点恢复](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/PointInTimeRecovery.html)。

# 建立你的独立用户群

建立你自己的订户名单是一项令人振奋的努力！无论你是打算创办一份时事通讯，发送新内容的通知，还是围绕你的工作创建一个社区，没有什么比一封我写给你的电子邮件更私人、更直接的了。

我鼓励您今天就开始用简单订阅来建立您的订户群！像我的大部分作品一样，它是开源的，免费供你个人使用。在 GitHub 库深入研究代码，或者在[官方网站](https://simplesubscribe.org)了解更多。