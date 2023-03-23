# 你应该开放你一直在做的 API

> 原文：<https://betterprogramming.pub/you-should-open-up-that-api-youve-been-working-on-b0313a4df9bc>

## 这个世界需要你正在努力的东西——但可能是以你无法预见的方式

![](img/b76670af188d45f400cc6e28283013ea.png)

内森·杜姆劳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为一名开发人员，现在是最好的时机。你可以访问数千篇文章、数百万个堆栈溢出问题和数十亿条与科技行业相关的推文。另外，GitHub 刚刚收购了 npm。

编码训练营是一件事。像云专家[和](https://acloud.guru/) [Udemy](http://udemy.com) 这样的网站正在颠覆传统教育。简而言之，如果你想写软件，你可以得到你需要的东西。

但是，信不信由你，我最兴奋的事情是开放 API。多亏了面向公众的开放 API 文档，我可以在我使用的几乎所有网站或应用程序上添加或操作数据。我所要做的就是注册一个 API 密钥。

当你开发自己的软件时，我鼓励你向前支付。OpenAPI 规范 3.0 在这里，你需要使用它。

为什么？好问题。让我告诉你。

# 这是行业标准

开发人员实际上不喜欢做专有的东西。他们唯一喜欢从事的专有工作是业务逻辑——他们如何解决一个独特的业务问题。但是另一方面，框架、定制引擎和神圣不可侵犯的模式…

在我们今天生活的快节奏世界中，我们必须使用更大的社区支持的东西。我们需要那些栈溢出文章、中等教程或快速推文来获得我们正在寻找的答案。

![](img/206c81bfabc9ecda43386dd8150e797b.png)

[LinkedIn 销售导航员](https://unsplash.com/@linkedinsalesnavigator?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

部落知识被接受的日子已经一去不复返了。如果我们有问题，我们不需要知道该去找谁来解决。我们应该能够在互联网上找到问题的答案。

开放 API 就是这样。它得到了社区的支持，并经历了几次变革，才取得了今天的成就。最初是一个名为 Swagger 的开源项目，OpenAPI 规范已经走过了漫长的道路。现在像谷歌、易贝和 Atlassian 这样的大公司都使用这个规范。

作为一名开发人员，你想学习适销对路的技能。作为一家公司，你希望有能力从街上雇佣一些人，并立即让他们做出贡献。

这是黄金标准。这是当今开发实践需要的。

# 这是你的文件

听说过减价吗？当然，你有——它无处不在。即使这个名字听起来不熟悉，你也见过。它只是一种标记语言，在纯文本视图中提供简单的文本格式。而且 OAS 3.0 到处都用。

您可以向等级库中的所有内容添加专业质量的格式化文本描述。端点描述、模式定义、头和路径参数都受益于 markdown 描述。

有些人将自文档化代码描述为涅槃。大家都知道开发人员不喜欢写文档。我们还知道，如果文档没有提前完成，那么大约 0%的机会是在事后编写的。

因此，拥有一个文档与源代码混合在一起的规范是你所能得到的最接近天堂的东西。编写新的端点？为什么不写几行 markdown 来描述它在做什么呢？这只需要一分钟，您的规范将获得专业质量可读性的好处。

# 它会验证你

曾经厌倦了在控制器的入口点中编写相同的转义条件吗？我觉得我个人已经编写了 1677 次以下代码的变体:

```
if(!event.someProperty || isNaN(event.someProperty)){
    return 400;
}
```

虽然这段代码简单且重复，但它是必要的。但是一定有一种更简单的方法，而不是在您的 web 服务中随意使用这种方法，对吗？

没错。

![](img/28b81c2ae4f38fd2dc4cf459543f070b.png)

[马太·亨利](https://unsplash.com/@matthewhenry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

使用一种易于使用的格式，您可以定义实体的形状——说明哪些字段是必需的，哪些是可选的，以及它们的数据类型是什么。如果您有特定的数据格式，您甚至可以定义一个正则表达式模式。

当您设置了规范来验证头、路径参数和主体时，验证将位于 API 网关之上，这意味着您的代码在收到带有有效参数的请求之前不会运行。

这很好，因为这意味着您不必花费时间编写琐碎的验证代码——这意味着您也不必编写那些单元测试。这意味着您也不必维护那些单元测试。此外，在规范中定义模式也为 API 文档的消费者提供了数据是如何形成的。

将请求验证添加到您的规范中，将有助于提高您的生产率并降低复杂性。

如果您正在使用任何 AWS 无服务器服务，如 lambda 和 API Gateway，OAS 3.0 的验证特别有用，因为它直到请求验证运行后才执行您的 lambda 代码，从而为您节省 Lambda 运行的成本。

# 它是可扩展的

OAS 3.0 已经做了很多开箱即用的工作。但是，如果您需要它来做一些特定的业务，没问题。需要它为您已经集成的服务做些什么吗？它可能也有一个文档化的扩展。

扩展的实际实现存在于您的代码或任何使用规范的东西中。但是你可以很容易地给它添加一个新的扩展名，只要你给它加上前缀`x-`。例如，如果我想创建一个新的扩展来自动更新我的 Postman 文档集合，我可以在规范中声明它，如下所示:

```
openapi: 3.0.0
info: 
  title: My Example OAS3.0 Spec
  description: Provide a meaningful example for my readers
  version: 1.0.0x-update-postman-documentation-collection:
  collectionName: Example OAS3.0 API
  autoIncrementVersionNumber: true
```

在我的构建管道中，我可以有一个 lambda 函数在成功时触发，它将查看规范，读取扩展，然后使用 Postman API 更新我定义的集合。

**注意:**扩展不会出现在生成的文档中，所以您可以放心地在其中放入您想要的东西。不过，我建议不要在源代码控制中添加 API 键和秘密。

# 它直接与 AWS 服务对话

![](img/49b027eb1200ac118e01ead9e89d30d0.png)

[杰森·罗斯韦尔](https://unsplash.com/@jasonrosewell?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

AWS 有一整套与 API Gateway 集成的现成扩展。它提供了一种简单的机制，将您的规范与真实的基础设施联系起来，并添加诸如 lambda 授权器、请求和响应转换，甚至直接代理诸如 DynamoDB 和 S3 之类的服务。支持的扩展列表可以在这里找到。

如果您使用 CloudFormation，部署脚本的一部分会读取 AWS 扩展并适当地配置您的 API 网关。无论是代理各种服务、配置 CORS，还是设置特定的集成参数，只需要四五行就可以设置好。

实际上，OAS 3.0 规范和 CloudFormation 脚本位于不同的文件中。CloudFormation 脚本引用了 OAS3.0 规范，并设置了您在部署时定义的所有内容。

# 它打破了界限

你是个聪明人。但是你可能还没有考虑到你的应用程序的每一个用例。你听说过无限猴子定理吗？

![](img/d8e92c662868be4aaa45072c3e7aff07.png)

Jolanda van der Meer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

一只猴子在打字机上随意敲击键盘无限长的时间，几乎肯定能打出任何给定的文本，就像威廉·莎士比亚的作品一样。

在这个比喻中，你是猴子。给你无限的时间，你可能会想出你的 API 的所有用例。但是我们没有无限的时间。

让我们将这段时间分配给无限多的开发人员。现在我们讨论的是大规模的发现！如果你的 API 是公开的，人们几乎肯定会想出你从未想过的方法来使用你的软件。

保持开放允许大规模创新。不要因为对终端保密而扼杀创新。

# 现在开始

是时候让世界变得更美好了。在新冠肺炎疫情的混乱中，我们看到了前所未有的机会来回馈和帮助我们的社区。

虽然公开 API 看起来没什么大不了的，但它使其他开发人员能够进行集成和自动化，否则这是不可能的。

通过提供有所作为的方法，帮助推动真正的变革。打开你的 API。为他人做贡献。我们都是一个团队的。

祝你好运，玩得开心，并产生影响！