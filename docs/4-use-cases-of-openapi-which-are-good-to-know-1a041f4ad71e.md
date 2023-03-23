# OpenAPI 简介

> 原文：<https://betterprogramming.pub/4-use-cases-of-openapi-which-are-good-to-know-1a041f4ad71e>

## OpenAPI 的 4 个使用案例，值得了解

![](img/b14f8e21749dd7cca7708f18bfd0df3e.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Gert RDA valasevi it](https://unsplash.com/@skraidantisdrambliukas?utm_source=medium&utm_medium=referral)拍摄的照片

在本文中，我将介绍 OpenAPI (OA)及其一些更有趣的用例。如果您已经熟悉什么是 OA，那么您可以直接跳到本文的用例部分。

## 什么是 OpenAPI？

OpenAPI 是一种规范，用于以某种方式记录基于 HTTP 的 API，这种方式允许用户(通常是从事 API 本身工作的团队之外的用户)理解它所公开的功能并与之交互。

该规范最初由一家名为“Swagger”的名为 [SmartBear](https://smartbear.com/?utm_medium=ppcg&utm_source=aw&utm_term=smartbear&utm_content=518273908799&utm_campaign=SEM_SmartBear:CoreTerm_BR_EMEA_ENG_EXT_Prospecting&awsearchcpc=1&gclid=Cj0KCQiAkZKNBhDiARIsAPsk0Wjjc7MtwLTepzsILVbeGCMM-h1sUv7bvJMZotn317gBfkthROuoW9waAvrkEALw_wcB&gclsrc=aw.ds) 的公司开发，成为 API 文档事实上的标准。这导致了 linux 基金会下的 [OpenAPI 倡议](https://www.openapis.org/about)的形成，它现在管理该规范，以及将该规范从 Swagger 重命名为 OpenAPI。因此，当你阅读 Swagger 规范和 OpenAPI 规范时，它们实际上是相同的——Swagger 只是 OpenAPI 的早期版本(3.x.x 版之前)。

## 创建规格

OA 规范通常采用`YAML`文件的形式，尽管也支持`JSON`。你可以在[这个库](https://github.com/OAI/OpenAPI-Specification)和[这里](https://github.com/snok/drf-openapi-tester/tree/master/tests/schemas/sample-schemas)看到规格的例子。

规范可以是单个文件，也可以是多个带有引用(`$ref`)的文件，引用使用路径指向文件或远程资源。如果使用引用，这些引用可以是相对/绝对文件路径，也可以是网络上资源的 URI。这使得 OA 规范非常有弹性，但一开始也有些混乱——我们的大脑还不能很好地适应解析 refs，这使得学习曲线有些陡峭。你可以在这里阅读更多关于这个特性的信息[。](https://swagger.io/docs/specification/using-ref/)

创建 OA 规范最简单的方法是写下一个. yml 文件或一堆这样的文件。尽管这将 API 规范的编写和维护与开发过程联系在一起，增加了开销。此外，当规范是手工编写时，这不可避免地会导致人为错误和规范与实际 API 不同步的情况。因此，这是应该尽可能避免的做法，除非有令人信服的理由这样做。

另一种方法是从代码中生成 OpenAPI 规范。

许多较新的后端框架都内置了某种程度的 OA spec 生成。例如，FastAPI (Python)将规范生成作为其核心功能的一部分，而 NestJS (TypeScript)有一个可选的核心扩展[。](https://docs.nestjs.com/openapi/introduction)

另一方面，旧的框架倾向于拥有丰富的生态系统，包括用于生成 OpenAPI 规范的库。例如参见:Django+DRF 的[本库](https://github.com/tfranzel/drf-spectacular)(Python)，Spring Boot 的[本库](https://springdoc.org/)(Java/Kotlin)，Rails 的[本库](https://github.com/rswag/rswag)(Ruby)，Laravel 的[本库](https://github.com/vyuldashev/laravel-openapi)(PHP)。

## 验证规格

OA specs 的创建越接近 API 代码，从所使用的语言中收集的类型信息越多，您就越能相信生成的 OA specs 反映了正在讨论的 API 端点。因此，反过来，人工干预越多，你就越不信任这些规范。

因此，根据实际的 API 测试规范是一个好主意——特别是如果您必须手工编写规范或者调整生成过程的话。对于一些框架来说，有一个专门的库用于这个目的，例如，参见 Django+DRF 的[这个库](https://github.com/snok/drf-openapi-tester)(我与人合著)，还有更多通用的测试解决方案——参见文章末尾的测试和调试用例了解更多细节。

另一个选择是验证规范是否写得正确，找出错误或者检查规范的风格。如果你使用一个实体库来为你生成规格，这可能是多余的。另一方面，如果您手工编写规范，或者在生成后修改它们，并提交到您的 VCS，那么这种形式的验证是必需的。

为此，有一个[官方 CLI 工具](https://www.npmjs.com/package/swagger-cli)你可以用 NPM/Yarn 安装。它的验证信息非常糟糕，难以理解——所以它不是最好的林挺工具。一个更优雅的工具是 [openapi-cli](https://github.com/Redocly/openapi-cli) ，同样使用 Node.js 作为主干。除非您有充分的理由使用官方 CLI，否则我会推荐您使用该工具。

提示:在 CI 中验证您的规范并使用预提交钩子通常是一个最佳实践。

# 用例:文档

当然，OA specs 的第一个也是最主要的用途是记录 API。然而，如果没有消费文档的方式，文档是没有用的。

为此，有多种工具可以生成用于显示 API 规范的 UI。

这个领域最古老的工具是 SmartBear 的 swagger-UI。它免费且简单易用，但在我看来，它的默认用户界面和样式并不怎么样。此外，它缺少下面讨论的其他工具所具有的许多花哨功能(例如高级搜索功能)。如果你不介意有一个相当基本的网站来展示你的规格，这是一个可靠的选择。

另一个非常流行的叫做 [redoc](https://github.com/Redocly/redoc) ，它是由维护`openapi-cli`项目的同一家公司(从开源项目发展而来)维护的，叫做 [Redocly](https://redoc.ly/) 。该工具提供了一个开箱即用的漂亮 UI——使用 CLI，您可以轻松地生成一个生产就绪的 React 驱动的网站，该网站具有良好的定制和漂亮简洁的外观，您可以在此处看到一个示例。如果你需要大量的品牌或更复杂的功能，你需要付费。

最后，还有 [RapiDoc](https://mrin9.github.io/RapiDoc/) ，这是一个开源项目，提供了很多选项。这是一个框架不可知的 JS 库，所以你可以在任何你喜欢的框架上使用它(比如 Vue，React 等等)。).您还可以完全控制样式，但也可以使用它的默认设置。这是一个更复杂的选项，需要开发时间，但是它在定制和用户控制方面比其他选项提供了更多。根据您的使用情况，这可能是一个不错的选择。

注意:上一节提到的许多库和框架至少捆绑了其中一种工具。因此，举例来说，FastAPI 提供了一个现成的 redoc 网站，而 DRF 壮观则同时提供了 Swagger-UI 和 redoc。因此，您应该提前检查这一点，并做出相应的决定。

# 用例:代码生成

因为 OA 规范是机器可读的，并且包含大量关于类型的信息，所以它们的主要用例之一是代码生成。您可以做很多事情——常见的例子是从 OA specs 生成客户端 SDK 和服务器存根，创建参考客户端，甚至创建要在应用程序前端使用的客户端脚手架。

有两个非常受欢迎的项目主导着这个领域，这两个项目都采用了有点类似的方法，使用 code-gen 的模板:

第一个项目是 [Swagger Codegen](https://github.com/swagger-api/swagger-codegen) ，同样由 SmartBear 完成。这个项目在 Github 上有 13K 颗星，非常稳定。尽管它的用途有所限制，它所支持的模板范围也是如此。

与 10K 明星合作的第二个项目是 [OpenAPI-Generator](https://github.com/OpenAPITools/openapi-generator) 。这个项目提供了广泛的工具——用 Java 编写——和大量的模板可供选择。它非常强大，拥有其他项目没有的能力，但是不太稳定(阅读，版本之间有突破性的变化)。

# 用例:类型脚本

如果您的项目包括使用 TypeScript 开发的前端组件，那么您可以使用 OA specs 做的最有用的事情之一就是从它们生成 TypeScript 类型。我在多个项目中这样做过，包括一次我自己写了一个 OA 到 TypeScript 的转换器。幸运的是，现在有两个成熟的开源生成器可以为您完成这项工作: [DTSGenerator](https://github.com/horiuchi/dtsgenerator) 和 [openapi-typescript](https://github.com/drwpow/openapi-typescript#readme) 。

如果您的项目有一个开发部署，我建议您执行以下操作:公开一个端点，该端点允许将 OA 规范作为文件下载。然后创建一个 shell 脚本，使用`curl`或`wget`下载规范，将结果传送到这些工具之一。例如，以下脚本以这样的方式使用 DTSGenerator:

这样，前端开发人员更新 API 规范变得非常容易——只需要一个简短的 shell 命令。

或者，您可以将生成的类型发布为内部 NPM 包，或者从存储库中安装它们。尽管这将控制从前端转移到了后端，但灵活性稍差。

# 用例:测试和调试

一种越来越流行的测试 API 的有趣方法是所谓的“模糊测试”。这是一种基于属性的测试，但针对的是 API 而不是函数。在模糊测试中，针对一系列随机数据测试给定的 API，识别弱点、未处理的错误等。虽然模糊测试的主要目的是识别安全性和代码弱点，但它也是一种确保 o a 文档准确反映其所代表的 API 的方法。

有些付费服务使用 OA 规范作为事实的来源，例如，Gitlab 在其终极计划中为客户提供了[服务](https://docs.gitlab.com/ee/user/application_security/api_fuzzing/)。但是，也有多种开源解决方案。 [Dredd](https://github.com/apiaryio/dredd) 是一个成熟的多平台项目，可以用于这个目的。在 Python 世界里，有[模式](https://github.com/schemathesis/schemathesis)，它建立在优秀的假设测试库之上，提供模糊测试。

# 结论

在本文中，我讨论了 OpenAPI 及其各个方面。以上是一个一般性的介绍，同时也提供了我觉得有趣和有价值的用例。OA 生态系统是动态的，并且在不断发展。我建议看看这个网站上的工具列表，以及这个令人敬畏的列表。