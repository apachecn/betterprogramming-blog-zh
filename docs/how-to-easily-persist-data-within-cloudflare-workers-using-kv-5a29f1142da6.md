# 如何使用 KV 在 Cloudflare Workers 中轻松持久化数据

> 原文：<https://betterprogramming.pub/how-to-easily-persist-data-within-cloudflare-workers-using-kv-5a29f1142da6>

## 学习在您的员工中使用 Cloudflare 的 Redis 式存储，免费帐户的使用限制非常宽松

![](img/34f681d850320ac1fb307dfafe8753fc.png)

马修·施瓦茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

> 这是关于 Cloudflare workers 的系列文章的第 2 部分，请在此处查看其他文章:
> 
> 步骤 1: [使用 Cloudflare Workers](/create-and-host-apis-for-free-using-cloudflare-workers-808830a4e5f5)
> 免费创建和托管 API 步骤 2:如何使用 KV 在 Cloudflare Workers 中轻松持久化数据

在上一篇文章中，我们使用 Cloudflare workers 创建了一个简单的 API 来检索动物。尽管 API 本身目前使用的是虚拟数据，这对于演示功能来说很好，但是对于真正的 API 来说没有用。

这就是 KV 来自 Cloudflare 的原因。这是一个简单、低延迟、键值存储。如果您曾经使用过 Redis，您会立即意识到它本质上提供了与 Cloudflare 自己的实施相同的功能。

在本文中，我将更详细地解释 KV 是什么，以及如何在您的 Cloudflare workers 中使用它。

# KV 是什么？

对于不熟悉 Redis 的人来说，KV 提供了一个简单的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 接口来读写数据。因为它是一个键值存储，所以可以使用给定的键进行读写，并且可以在该键下存储任何数据，从简单的字符串到 JSON。

最重要的是，作为一个免费用户，你可以得到一个相当慷慨的使用计划。您最多可以发送 100，000 个读取请求和 1，000 个写入、列出和删除请求(每种类型 1，000 个)。存储方面，最多可以存储 1GB。

如果你的 API 开始流行，他们的价格会比免费层更合理。对于其他类型，每 1000 万次读取只有 0.50 美元，每 100 万次请求只有 5 美元。

KV 最终是一致的，这意味着在您的数据被写入到可供您的工作人员的每个调用者使用之间可能会有延迟。这意味着，如果两个 API 调用几乎同时写入同一个键，您可能会遇到竞争情况。

当使用 KV 时，这是需要记住的事情，尤其是如果您的 API 是写密集型的，因为 KV 最适合读密集型的应用程序或用例。它作为一个缓存运行，但是它作为简单 API 的存储选项工作得非常好。

准备好为您的 API 添加持久性了吗？我们来看看如何！

# 创建一个 KV 名称空间

在您的 worker 中使用 KV 之前，我们必须首先创建一个 KV 名称空间。这只是一个名字的别称。

要创建新的 KV 名称空间，请执行以下步骤:

1.  前往[https://dash.cloudflare.com/](https://dash.cloudflare.com/)并登录您的账户。
2.  单击左侧菜单上的工人旁边的小箭头，然后单击 KV。
3.  将有一个标记为“创建名称空间”的按钮。点击它。
4.  为您的名称空间键入一个描述性名称，然后单击添加。

这就是全部了。现在，您已经创建了一个 KV 名称空间来存储您喜欢的任何数据。记下 ID(它旁边有一个方便的复制按钮)，因为我们在下一步中需要它。

我们需要让您的工作人员能够访问 KV 名称空间，然后才能使用它，所以接下来让我们这样做。

# 将您的 KV 名称空间绑定到一个 Worker

现在我们已经创建了一个 KV 名称空间，我们可以将这个 KV 名称空间绑定到任意数量的 workers。我们只将它绑定到一个，但是没有理由不能将同一个名称空间绑定到多个 workers。

因为 Cloudflare worker 在无服务器的运行时中运行，所以每当调用它时，绑定会有效地将 KV 名称空间注入到 cloud flare worker 中。很酷，对吧？

将 worker 绑定到名称空间很简单，打开`wrangler.toml`并在底部添加以下几行:

```
kv_namespaces = [
  { binding = "ANIMALS", id = "your-id-here" }
]
```

您需要将`your-id-here`替换为创建名称空间时给出的 ID，该 ID 始终可以在您的 Cloudflare 仪表板中的 KV 页面上看到。

至于`binding`，您可以给它起任何您喜欢的描述性名称，但是它将是您在代码中用来访问您的 KV 名称空间的名称。

我们目前只绑定一个 KV 名称空间，但是如果您想要多个名称空间，`kv_namespaces`接受一个对象数组。

## 处理开发和生产数据存储

您可能想知道我们如何在开发和生产中使用不同的数据存储来测试我们的员工，这将是一个很好的问题。

Cloudflare 的工具非常好，所以如果您现在使用`npm run deploy`部署您的员工，一切都会很好。然而，如果您运行`npm start`以开发模式启动您的 worker，将会出现如下错误:

```
✘ [ERROR] In development, you should use a separate kv namespace than the one you'd use in production. Please create a new kv namespace with "wrangler kv:namespace create <name> --preview" and add its id as preview_id to the kv_namespace "ANIMALS" in your wrangler.toml
```

有益的是，它甚至为我们提供了问题的解决方案，并让我们有机会尝试使用 Wrangler CLI 创建 KV 名称空间的替代方法。

让我们按照错误消息所描述的那样，运行以下命令:

```
wrangler kv:namespace create <name> --preview
```

与之前类似，您需要给它一个描述性的名称。`--preview`选项告知牧马人它仅用于预览，本质上是开发模式。一旦完成，它将输出您的新预览 KV 名称空间及其 ID。我们现在可以像这样更新`wrangler.toml`:

```
kv_namespaces *=* [
  { binding *=* "ANIMALS", id *=* "production-id", **preview_id *= "*preview-id"** }
]
```

我加粗了前一行代码的变化，本质上，我们提供了一个额外的参数，它是预览 KV 名称空间的 ID。当您的 worker 启动时，将根据它运行的环境使用正确的 KV 名称空间，因此无需担心开发与生产的冲突！

我们现在可以在代码中使用 KV 名称空间。让我们来看看是如何做到的。

# 如何在代码中访问 KV 名称空间

既然我们的工人可以访问我们在运行时创建的 KV 名称空间，那么我们如何访问这个名称空间呢？

如果您习惯于更传统的工作流，这可能是您希望必须配置用户名和密码连接的地方，但 Cloudflare workers 并非如此。

所有这些工作都由 Cloudflare 为我们完成，因此我们可以专注于有趣的事情，例如构建更多的 API！

让我们看一下使我们的工作人员能够访问 KV 名称空间的代码更改:

`src/index.ts`

```
export interface Env {
   //This will be auto-populated with the KV Namespace that is bound in the wrangler.toml
   //and exposes all the methods you'll need (get, put, list etc.)
   **ANIMALS: KVNamespace;**
}export default {
   async fetch(
      request: Request,
      **env: Env,**
      ctx: ExecutionContext
   ): Promise<Response> {
      return await router.handle(request, env)
   }
};
```

我把重要的线加粗了，但是只有一条是新的。首先，在 fetch 函数中，它是我们的 worker 的入口点，我们接收一个名为`env`的参数。此参数包含环境变量，在许多情况下，Cloudflare 会为我们自动填充这些变量。

KV 名称空间就是这种情况，因为 Cloudflare 将用您的绑定中使用的名称填充`env`。在我上面的例子中，那是`ANIMALS`，所以我可以使用`env.ANIMALS`访问 KV 名称空间。

因为这是 TypeScript，所以我们要描述一下`env`里面有什么(里面有一个`Env`的类型，上面可以看到)。同样，Cloudflare 提供了与其工具交互所需的类型。

因此，在我们定义的`Env`接口中，我添加了`ANIMALS`作为属性，其类型为`KVNamespace`。

有了这个改变，我们将能够使用`env.ANIMALS`访问 KV 名称空间，并在其上调用方法来读取和写入数据。现在，我们可以更新 API 的代码来使用 KV 名称空间。

# 更新 API 以从您的 KV 名称空间读取和写入数据

我们现在已经创建了一个 KV 名称空间，允许我们的 worker 访问，并通知我们的代码 KV 名称空间已经存在。与在常规应用程序中设置数据库连接的痛苦相比，这相当简单，尤其是考虑到它自动为我们处理不同的环境！

继续围绕动物构建 API 的例子，让我们更新 API 的实现，以便能够从 KV 名称空间返回动物，以及创建新的动物并将其存储在 KV 名称空间中。

## 添加读写数据的函数

为了简单起见，我将所有代码都添加到了`src/index.ts`中，但是您可能希望将它拆分到其他文件中，以使其更加整洁。首先，让我们添加一些函数来帮助我们与 KV 名称空间进行交互:

这些是围绕 KV 名称空间的简单包装器，允许我们的代码检索所有动物，更新动物，并添加单个动物。

我们不能在 KV 中存储原生类型，比如对象，所以在存储数据时必须序列化到 JSON，或者在读取数据时从 JSON 反序列化。如果存储的是简单的字符串，可以直接使用 KV 以外的值。

我选择将所有数据作为 JSON 中的一个对象数组存储在一个键下，但是如何安排数据取决于您。例如，我可以选择将每个动物存储为自己的键。

如您所见，KV 有一个简单的接口，它公开了`put`来存储数据，公开了`get`来读取数据。这里我不需要它们，但是您也可以使用`delete`删除数据，并使用`list`列出存储在名称空间中的所有键。

如果我将每种动物存储为自己的键，那么后者对于显示动物列表会特别有帮助。如果我们只允许同一个动物被创造一次而不被更新，这也是避免竞争条件的更好选择。不过，这确实使代码变得更加复杂，所以我想在这个演示中尽可能保持简单。

## 更新 API 路线

最后一步是更新我们的 API 路由，以使用新定义的方法。这段代码仍在`src/index.ts`中，非常简单:

前两个端点之前已经存在；我刚刚更新了它们来读取 KV 的数据。最后一个端点 POST 是新的。它允许我们用 JSON 主体向`POST /animals`发送请求，如下所示:

```
{
 "name": "chicken",
 "type": "bird"
}
```

一旦请求被设置，一个新的动物将在 KV 中被创建。然后，您可以通过点击`GET /animals`来查看所有动物，并通过点击`GET /animals/:id`使用响应中返回的任何 ID 来检索单个动物。

如果你正在寻找一个方便的桌面应用程序来与你的 API 进行交互，我建议试试 Postman。

这就是全部了。您现在可以使用`npm start`运行我们的 worker(或者使用`npm run deploy`将其部署到生产环境中)并查看您的 API 的运行情况，包括数据存储！

# 摘要

如果您一直坚持下去，希望您现在已经有了一个具有工作数据存储的 API，并且仍然可以免费使用 Cloudflare(除非您的 API 变得非常受欢迎，但是这是一个好问题！).

当使用 KV 时，您可能会看到不同的延迟时间，因为您的数据存储在中央数据中心，并且更多地在本地缓存。因此，根据它重试信息的位置，响应时间可能会有所不同，但根据我的经验，在生产中仍然总是在 150 毫秒以内。此外，您的数据被访问得越频繁，从缓存中返回的可能性就越大。

如果你需要 KV 提供的更强的一致性保证，我推荐你看看他们的[持久对象](https://developers.cloudflare.com/workers/learning/using-durable-objects/)。它不会以最终一致的方式运行，因为在同一时间只有一个对象存在。然而，有一个小成本。

我现在使用 Cloudflare 作为我所有小型辅助项目的首选，快速、廉价地开发 API 和网站原型。我认为他们的工具非常容易使用，他们在免费层的使用率非常高。

关于更多的阅读，我推荐阅读 [KV 文档](https://developers.cloudflare.com/workers/runtime-apis/kv/)或者更深入地阅读 [KV 如何工作](https://developers.cloudflare.com/workers/learning/how-kv-works/)。你可以在这里看到完整的源代码[，在这里](https://github.com/apeacock1991/cloudflare_worker_api/tree/with_persistence)看到这个 API 版本和之前版本[之间的变化。](https://github.com/apeacock1991/cloudflare_worker_api/compare/with_persistence)

```
**My book, Creating Software with Modern Diagramming Techniques, is out now!**[Click here](https://pragprog.com/titles/apdiag/creating-software-with-modern-diagramming-techniques/) to learn how to create diagrams to communicate information more directly and clearly than words ever can. Using only text-based markup, powered by Mermaid, create meaningful and attractive diagrams to document your domain, visualize user flows, reveal system architecture at any desired level, and much more!
```