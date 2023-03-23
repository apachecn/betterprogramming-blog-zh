# 如何用契约确保可靠的服务间通信

> 原文：<https://betterprogramming.pub/how-to-ensure-reliable-inter-service-communication-with-contracts-a30436fac1d8>

## 利用几个开源工具来防止服务之间的破坏性变化

![](img/314b72e646dd48ba82e8e891866aeabd.png)

[Cytonn 摄影](https://unsplash.com/@cytonn_photography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

您刚刚部署了对您的团队拥有的 API 的更改，该 API 由几个内部客户使用。所有的测试都是绿色的，但是当部署时，几个客户端的集成突然开始产生错误！

在我们生活的 API 世界中，这是一个极其常见的场景。我们可以使用一些技术来帮助更快地检测服务之间的问题，它们以契约的形式出现。

# 什么是合同？

在商业世界中，合同定义了两家公司如何做生意。他们都列出了条款并达成协议——A 公司将向 B 公司提供 X，B 公司将 Y 返还给 A 公司。

软件也是如此，只是规模更小(通常编写的代码更少！).实现将根据您使用的编程语言而有所不同，但在大多数现代语言中，它们通常采用接口的形式。

同样，在商业世界里，契约也可能被打破。如果你将一个方法的返回值从整数改为字符串，那么你已经破坏了与任何客户的契约，你的应用程序现在很有可能会崩溃。

在静态类型语言中，比如 Java，类型系统强制执行这些契约——如果你做了上面描述的改变，它不会编译，因为调用类将显式地期望一个整数。

迅速得到有东西坏了的反馈是至关重要的。这种快速的反馈会导致更少的错误，更快的开发，并且让其他不熟悉特定代码库的工程师更容易理解正在发生的事情。

这对于单个服务的开发来说很好，但是我们如何利用两个或更多服务之间的契约呢？

# **用 OpenAPI 定义 HTTP APIs 的契约**

OpenAPI 规范，以前被称为 Swagger，已经存在了将近 10 年。它通常与 REST APIs 一起使用，但是没有理由不能将它用于非 RESTful APIs，因为它只是允许您描述自己的 API。

您可以描述服务公开的端点、这些端点期望的参数以及从这些端点返回的响应的格式。下面是 OpenAPI 中检索订单的 API 端点示例:

这个特殊的模式是用 YAML 写的，但是你也可以用 JSON 写——我个人觉得 YAML 比 JSON 可读性更好。您也可以使用诸如 [Swagger Editor](https://editor.swagger.io/) 之类的工具来生成一个漂亮的基于 web 的 API 视图。

## 带光谱的 OpenAPI 林挺

首先，我们应该确保我们的 OpenAPI 规范是有效的。OpenAPI 有很多工具，其中之一就是 [Spectral](https://levelup.gitconnected.com/how-to-create-consistent-user-friendly-api-documentation-with-spectral-a2faa35e1240) 。

Spectral 是 OpenAPI 的林挺工具。这将确保您的 OpenAPI 定义符合 OpenAPI 规范。此外，您可以定义您自己的规则，您可能希望在您的 API 上实施这些规则，以确保一致的风格指南。

## **根据 OpenAPI 模式验证您的 API**

OpenAPI 文档本身对于任何试图与您的 API 集成的人都非常有用。但是，如何确保您编写的代码符合这种模式呢？

这就是德雷德的用武之地。一旦安装和配置完成，它就可以作为自动化测试套件的一部分运行(或者在本地运行),以验证您的 API 响应对于您定义的 OpenAPI 模式是有效的。

当您对实现或模式进行更改时，这将为您提供快速的反馈。Dredd 本质上是运行端到端的测试，但是它们是非常轻量级的，因为 Dredd 完成了大部分繁重的工作。

您可以在这里开始使用 Dredd [，一旦设置好，您需要做的就是在您的 OpenAPI 文件中定义示例请求，Dredd 会处理剩下的事情。](https://dredd.org/en/latest/installation.html)

您的 API 可能需要某种形式的状态来进行一些测试(例如 GET 请求)，所以 Dredd 提供了[钩子](https://dredd.org/en/latest/hooks/index.html)来允许您加载测试夹具。

# 与 Pact 的合同测试

契约允许你在两个服务之间生成一个共享协议。该协议概述了特定客户端将发送的请求，以及特定提供商将返回的响应。

一旦生成，协议可以在服务和双方编写的测试之间共享，以确保协议不被破坏。每次代码更改都会运行这些测试，例如，如果任一方的请求或响应发生了更改，导致约定不再有效，那么测试就会失败，拥有这两个服务的团队需要进行沟通来解决问题。

集成了新 API 的两个服务的典型 Pact 工作流如下所示:

1.  两个团队都将同意 API 的契约——请求和响应。
2.  API 的提供者将记录这个合同。例如，作为 OpenAPI 规范。
3.  客户将首先编写合同测试，并生成一个契约。
4.  客户端将编写他们的集成代码，使用契约来验证他们的请求是否有效。
5.  提供者将为 API 编写代码，使用客户机生成的契约来确保它们产生的东西与客户机期望的相匹配。

一旦就位，所有的测试都是绿色的，契约就通过了验证，并且应该在任何代码合并到 master 之前自动运行。如果协议以任何方式被破坏，测试将会失败，团队将需要了解原因。如果 API 需要更改，客户端将需要为更新的 API 生成新的契约，然后提供者可以更新其 API 以进行匹配。

这种方法的另一个好处是允许客户和提供者在他们各自的服务上同时编写代码。这是因为，一旦步骤 3 完成，两个团队都有契约来验证他们编写的代码是否符合他们之前达成的契约。

虽然不是必需的，PACT 还提供了一个[存根服务器](https://github.com/pact-foundation/pact-stub-server)，客户端可以使用它来接收存根响应——例如，这可以用于手动测试。

最后，契约的共享可以手动完成，但是 PACT 提供了一个[代理](https://docs.pact.io/getting_started/sharing_pacts/)来使生活变得更容易。一旦集成，客户和提供者都可以从代理发布和检索契约。

请记住，只有当您同时控制客户端和提供商时，Pact 才是合适的，因此不应该与您没有直接所有权的第三方服务一起使用。

# 关键要点

*   始终使用 OpenAPI 之类的规范来记录您的 API
*   利用 Spectral 和 Dredd 等工具来增强 API 规范的功能
*   使用 PACT 减少客户和提供商之间的错误和重大变更

```
**Want to Connect With the Author?**Looking to expand your technical knowledge but not sure what to read? I run a free newsletter providing fortnightly technical book recommendations, including my key takeaways from the books. Interested? [Sign up here!](https://subscribe.technicalbookclub.com/?utm_source=medium&utm_medium=article&utm_campaign=interservicecontracts)
```