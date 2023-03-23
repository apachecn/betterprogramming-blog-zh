# 如何构建 Pastebin 克隆 Web 服务

> 原文：<https://betterprogramming.pub/how-to-build-a-pastebin-clone-web-service-3503917ec4bd>

## 免费设计、构建和部署无服务器粘贴工具

![](img/5fb954761dac29af990ad4416f3006d4.png)

凯利·西克玛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

今天，我们将构建一个 [Pastebin](https://pastebin.mozilla.org/) 克隆——一个允许用户上传和分享文本或代码的网络服务，通过被称为“粘贴”的链接。接下来是我如何通过 [Cloudflare Worker](https://workers.cloudflare.com/) 使用无服务器函数创建 Pastebin 克隆的旅程。如果您不熟悉 Pastebin，我强烈建议您在继续阅读之前尝试一下。

“为什么是帕斯捷宾？”你可能会问。嗯，通过聊天应用程序发送超过 50 行长的文本(*看着你，* [*IRC*](https://en.wikipedia.org/wiki/Internet_Relay_Chat) )并不是最好的沟通方式。

## TL；速度三角形定位法(dead reckoning)

*   使用 Cloudflare Worker 和 KV 构建 Pastebin 克隆
*   项目要求和限制计划
*   使用密钥生成服务(KGS)粘贴 URL UUID 生成逻辑
*   GraphQL API 的设计与实现
*   paste.jerrynsh.com[的现场演示](https://paste.jerrynsh.com/)
*   [GitHub 库](https://github.com/ngshiheng/paste-story)

这个 Pastebin 克隆的设计与[构建 TinyURL 克隆](/how-i-built-my-own-tinyurl-like-service-using-serverless-computing-f41899f3fdcb)非常相似，除了我们需要存储粘贴内容，而不是原始的非精简 URL。

在我们开始之前，这不是关于以下内容的教程或指南:

*   如何应对实际的系统设计面试
*   构建一个商业级的粘贴工具，如 [Pastebin](https://en.wikipedia.org/wiki/Pastebin) 或 [GitHub Gist](https://gist.github.com/)

相反，这是一个概念证明(POC ),说明如何通过 Cloudflare Worker 使用无服务器计算来构建一个简单的粘贴工具。要继续阅读本文，请查看本[入门指南](https://developers.cloudflare.com/workers/get-started/guide)的步骤 1 至 3。

我们走吧！

# 要求

让我们从阐明我们项目的用例及约束开始。

## 功能的

*   每当用户输入一段文本(或代码)时，我们的 web 服务应该生成一个带有随机关键字(UUID)的 URL，例如`paste.jerrynsh.com/aj7kLmN9`
*   每当用户访问生成的 URL 时，用户应该被重定向以查看原始的粘贴内容，即原始的文本块
*   粘贴的链接应该在 24 小时后过期
*   UUID 应该只包含字母数字字符( [Base62](https://en.wikipedia.org/wiki/Base62) )
*   我们的 UUID 长度应该是 8 个字符

## 非功能性

*   低延迟
*   高度可用

## 预算、容量和限制规划

和我们之前的尝试一样，我们的目标是免费提供这项服务。考虑到 [Cloudflare 工作人员的定价](https://developers.cloudflare.com/workers/platform/pricing)和[平台限制](https://developers.cloudflare.com/workers/platform/limits)，我们的约束条件是:

*   每天 10 万个请求，每分钟 1 千个请求
*   CPU 运行时间不超过 10ms

类似于 URL 缩短器，我们的应用程序预计会经历高读写比率。也就是说，我们将使用 [Cloudflare KV](https://developers.cloudflare.com/workers/learning/how-kv-works) (下面是 KV)，这是一个用于这个项目的低延迟键值存储。

在撰写本文时，KV 的[自由层具有以下](https://developers.cloudflare.com/workers/platform/pricing#workers-kv)[限值](https://developers.cloudflare.com/workers/platform/limits#kv-limits):

*   10 万次阅读/天
*   1k 写入/天
*   1 GB 的存储数据(密钥大小为 512 字节；25 MiB 的值大小)

## 我们能储存多少浆糊

在这一节中，我们将估计我们的 Pastebin 克隆可以存储多少粘贴，考虑到上面的限制。与存储 URL 不同，存储文本块会消耗更多的空间(相对而言)。以下是我们将要做出的假设:

*   1 个字符为 1 个字节(使用此[字节计数器](https://mothereff.in/byte-counter)
*   假设平均而言，一个粘贴(文件)可以包含大约 200 行代码(文本)，这意味着每个粘贴的大小大约为 10 KB
*   最大存储容量为 1 GB，这意味着我们的 Pastebin 克隆最多只能存储 100，000 个粘贴

请注意，限额是基于每个账户的。

# 存储和数据库

## Cloudflare 工人 KV

对于这个概念验证，我们将选择 KV 作为我们的数据库。让我们更深入地了解一下它的功能。

目前， [CAP 定理](https://www.ibm.com/cloud/learn/cap-theorem)常用于对分布式数据存储进行建模。CAP 定理指出，分布式系统只能提供以下三种保证中的两种([来源](https://blog.cloudflare.com/workers-kv-is-ga/)):

1.  一致性—我的数据在哪里都一样吗？
2.  **可用性—我的数据是否总是可访问的？**
3.  **P** 分区容忍度—我的数据对区域性中断有弹性吗？

在 KV 的情况下，Cloudflare 选择保证 **A** 可用性和 **P** 分区容差，这符合我们的非功能性要求。尽管这种组合叫嚣着最终的一致性，但这是一种我们可以接受的折衷。

别忘了，KV 支持极高的读取量和超低延迟，非常适合我们的高读写比应用。

现在我们已经了解了权衡，让我们继续！

# 履行

## URL 生成逻辑

粘贴网址 UUID 生成逻辑将非常类似于网址缩写。以下是可能的方法的快速总结:

1.  使用 UUID 生成器为每个新请求按需生成一个 UUID
2.  使用粘贴内容的散列( [MD5](https://www.md5hashgenerator.com/) )作为我们的 UUID，然后使用散列的前 N 个字符作为我们的 URL 的一部分
3.  使用哈希+ Base62 编码的组合
4.  使用自动递增的整数作为我们的 UUID

但是，我们将采用上面没有提到的另一种解决方案。

## 预生成 UUID 键

对于此 POC，我们将使用单独的工作线程预先生成 KV 中的 UUID 列表。我们将把这个工作者称为关键生成服务(KGS)。每当我们想要创建一个新的粘贴，我们将分配一个预先生成的 UUID 给新的粘贴。

那么，这样做有什么好处呢？

使用这种方法，我们将不必担心密钥重复或哈希冲突(例如方法 2 或 3)，因为我们的密钥生成器将确保插入 KV 的密钥是唯一的。

这里，我们将使用 2kv:

*   `KEY_KV` —由我们的 kg 用来存储预先生成的 UUID 列表
*   `PASTE_KV` —由我们的主应用服务器用来存储一个键值对；其中键是 UUID，值是粘贴的内容。

要创建 KV，只需使用 Wrangler CLI 运行以下命令([源](https://developers.cloudflare.com/workers/cli-wrangler/commands#kv))。

```
# Production namespace:
wrangler kv:namespace create "PASTE_DB"
wrangler kv:namespace create "KEY_DB"# This namespace is used for `wrangler dev` local testing:
wrangler kv:namespace create "PASTE_DB" --preview
wrangler kv:namespace create "KEY_DB" --preview
```

为了创建这些 KV 名称空间，我们需要更新我们的`wrangler.toml`文件，以相应地包含名称空间绑定。要查看您的 KV 仪表板，请访问`https://dash.cloudflare.com/<your_cloudflare_account_id>/workers/kv/namespaces`。

## 如何生成 UUID

为了让 kg 生成新的 UUIDs，我们将使用`[nanoid](https://github.com/ai/nanoid)`包。如果你迷路了，你可以参考 GitHub 库的`[/kgs](https://github.com/ngshiheng/paste-story/tree/main/kgs)`文件夹。

KGS 怎么知道有没有复制的钥匙？每当 KGS 生成一个密钥时，它应该总是检查 UUID 是否已经存在于`KEY_DB`和`PASTE_DB`中。

此外，UUID 应该从`KEY_DB`移除，并在生成新膏时在`PASTE_DB`创建。我们将在 API 部分介绍代码。

## 要生成的唯一密钥不足

我们可能遇到的另一个潜在问题是——当我们的`KEY_KV`中的所有 UUIDs 完全用完时，我们该怎么办？

为此，我们将设置一个 [Cron 触发器](https://developers.cloudflare.com/workers/platform/cron-triggers)，每天定期补充我们的 UUID 列表。为了响应 Cron 触发器，我们必须向 Workers 脚本添加一个`["scheduled"](https://developers.cloudflare.com/workers/runtime-apis/scheduled-event)` [事件](https://developers.cloudflare.com/workers/runtime-apis/scheduled-event)监听器，如下面的代码所示。

由于我们的 POC 每天最多只能支持 1k 次写入，我们将把`MAX_KEYS`设置为生成 1000 次。请根据您的帐户限制随意调整。

# 应用程序接口

在高层次上，我们可能需要 2 个 API:

*   创建粘贴内容的 URL
*   重定向到原始粘贴内容

对于这个 POC，我们将使用 [Apollo GraphQL](https://www.apollographql.com/) 服务器在 [GraphQL](https://graphql.org/) 中开发我们的 API。具体来说，我们将在使用`[workers-graphql-server](https://github.com/cloudflare/workers-graphql-server)`的同时使用`[itty-router](https://github.com/kwhitley/itty-router)`工人模板。

在我们继续之前，如果您不熟悉 GraphQL，您可以通过 [GraphQL playground](https://paste.jerrynsh.com/graphql) 端点直接与这个 POC 的 GraphQL API 进行交互。

丢失时，你可以随时查阅`[/server](https://github.com/ngshiheng/paste-story/tree/main/server)`文件夹。

## 按指定路线发送

首先，我们的 API 服务器的入口点位于`src/index.js`，这里所有的路由逻辑都由`itty-router`处理。

## 创建粘贴

通常，要在 GraphQL 中创建任何资源，我们需要一个[突变](https://graphql.org/learn/queries/#mutations)。在 REST API 世界中，要创建的 GraphQL 变体非常类似于向 POST 端点发送请求，例如`/v1/api/paste`。下面是我们的 GraphQL 变体的样子:

```
mutation {
  createPaste(content: "Hello world!") {
    uuid
    content
    createdOn
    expireAt
  }
}
```

在幕后，处理程序(解析器)应该调用从 HTTP JSON 主体接收`content`的`createPaste`。此端点应返回以下内容:

```
{
  "data": {
    "createPaste": {
      "uuid": "0pZUDXzd",
      "content": "Hello world!",
      "createdOn": "2022-01-29T04:07:06+00:00",
      "expireAt": "2022-01-30T04:07:06+00:00"
    }
  }
}
```

你可以在这里查看 GraphQL 模式。

下面是我们的解析器的代码实现:

为了减少垃圾邮件，我们还添加了一个小检查来防止空粘贴的创建。

# 粘贴创建数据源

我们将与数据库(KV)交互的 API 逻辑保留在`[datasources/](https://github.com/ngshiheng/paste-story/blob/main/server/src/datasources/)`中。

如前所述，我们需要从我们的 KGS `KEY_DB` KV 中删除所使用的密钥，以避免为新粘贴分配重复密钥的风险。

在这里，我们还可以设置我们的键，在创建粘贴时有一天的`[expirationTtl](https://developers.cloudflare.com/workers/runtime-apis/kv#creating-expiring-keys)`:

类似地，我还创建了一个`getPaste` [GraphQL 查询](https://graphql.org/learn/queries/)来通过 UUID 检索粘贴内容。我们不会在本文中涉及它，但是可以在[源代码](https://github.com/ngshiheng/paste-story/tree/main/server/src)中查看它。在[操场上试一试](https://paste.jerrynsh.com/graphql):

```
query {
  getPaste(uuid: "0pZUDXzd") {
    uuid
    content
    createdOn
    expireAt
  }
}
```

在本次概念验证中，我们不支持删除任何贴膏，因为贴膏会在 24 小时后过期。

# 获取浆糊

每当用户访问一个粘贴 URL (GET `/:uuid`)粘贴的原始内容应该被返回。如果输入了无效的 URL，用户应该会得到一个丢失的错误代码。点击查看完整的 HTML [。](https://github.com/ngshiheng/paste-story/blob/main/server/src/handlers/paste.js#L4)

最后，要在本地启动开发 API 服务器，只需运行`wrangler dev`

# 部署

在发布您的代码之前，您需要编辑`wrangler.toml`文件(在`server/` & `kgs/`内)并在其中添加您的 Cloudflare `account_id`。你可以在[官方文档](https://developers.cloudflare.com/workers/get-started/guide#7-configure-your-project-for-deployment)中找到更多关于配置和发布你的代码的信息。

一定要确保 KV 名称空间绑定也被添加到您的`wrangler.toml`文件中。

要向您的 Cloudflare Worker 发布任何新的更改，只需在相应的服务中运行`wrangler publish`。

要将您的应用程序部署到自定义域，请查看这个短片。

## CI/CD

在 [GitHub 存储库](https://github.com/ngshiheng/atomic-url)中，我还使用 GitHub 动作建立了一个 CI/CD 工作流。要使用`Wrangler actions`，请将`CF_API_TOKEN`添加到您的 GitHub 库机密中。

您可以使用`Edit Cloudflare Workers`模板[创建您的 API 令牌](https://dash.cloudflare.com/profile/api-tokens)。

# 结束语

我没想到这个概念验证花了我这么长时间来编写和完成。像我之前的帖子一样，我很想以一些未来可以做出的*潜在的*改进(或者永远吸进积压的黑洞)来结束这篇文章:

*   允许用户设置自定义到期时间
*   粘贴编辑和删除
*   语法突出显示
*   分析学
*   带密码保护的私人粘贴

像网址缩写一样，粘贴工具也有一定的缺陷——这两种工具都使网址变得不透明，这是垃圾邮件制造者喜欢滥用的。好吧，至少下次你问“为什么这个代码不能工作？”，你就有自己的粘贴工具可以用了！

```
**Want to Connect With the Author?**This article was originally published at [jerrynsh.com](https://jerrynsh.com/how-to-build-a-pastebin-clone-for-free/)
```