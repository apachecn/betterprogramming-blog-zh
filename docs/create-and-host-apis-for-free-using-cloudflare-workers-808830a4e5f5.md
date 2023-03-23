# 使用 Cloudflare Workers 免费创建和托管 API

> 原文：<https://betterprogramming.pub/create-and-host-apis-for-free-using-cloudflare-workers-808830a4e5f5>

## 让您的 API 在几分钟内启动并运行，每天多达 100，000 个免费请求

![](img/f1610f7722229806dc396765d42b8cfe.png)

[通产省](https://unsplash.com/@mitifotos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

您有多经常不得不部署一个项目，比如一个 API，并且陷入如何在 AWS 中启动和运行它的困境，或者很快达到您帐户的自由层限制？

这发生在我为一个我参与的不和谐服务器开发机器人的时候。我想专注于构建我的机器人，能够快速迭代和部署，最终不用担心它如何从我的 Mac 转移到公众可以访问的地方。

进入 Cloudflare 及其工作人员。简而言之，它们允许您快速轻松地将代码部署到无服务器运行时。一旦设置完成(这本身只需要几分钟)，你就可以在几秒钟内从你的 Mac 上随时部署你的代码。

您也不需要担心使用情况，至少在早期不会，因为 Cloudflare 的免费帐户每天可以向您的员工提供多达 100，000 个请求，其中您的帐户上最多可以有 30 个。

卖了？让我们让你和你的第一个工人一起开始工作吧！

# 设置您的第一个 Cloudflare Worker

在部署任何东西之前，我们需要使用 Wrangler 创建一个新项目，Wrangler 是 Cloudflare 的 CLI 工具，用于与工作人员进行交互。任何时候您想要更新或部署您的员工，您都可以使用 Wrangler。

我们将创建一个基于 TypeScript 的 worker，它最容易启动和运行。但是，您可以用任何语言创建 workers，无论是编译成 [WebAssembly](https://webassembly.org/) (比如 Rust)还是 JavaScript(比如 Python、PHP 和 FSharp)。你可以在这里看到支持语言的完整列表。

以下是最初设置您的第一个 Cloudflare worker 的步骤:

1.  前往[https://dash.cloudflare.com/sign-up](https://dash.cloudflare.com/sign-up)创建一个免费账户。确保在尝试后续步骤(如部署)之前验证您的电子邮件地址。
2.  使用这些说明安装最新版本的 NodeJS。
3.  导航到您想要创建项目的位置，并运行`npx wrangler init my-project-name`。这将创建一个与最后一个参数同名的新文件夹。
4.  你会被问到一些问题。Git 上的第一个问题由您决定，但是后两个问题应该回答是，因为您想要一个`package.json`，并且您想要使用 TypeScript。如果您更喜欢普通的 JS，您可以选择退出 TypeScript，但是本文的其余部分将使用 TypeScript。
5.  接下来，选择`Fetch Worker`。作为参考，fetch worker 按需为 HTTP 请求提供服务(例如，您访问 URL)，而 Scheduled Worker 按照定义的时间表执行，类似于 [cronjob](https://en.wikipedia.org/wiki/Cron) 。

这就完成了您的 worker 的设置！现在，您应该有一个包含一系列文件的文件夹，接下来我们将仔细研究这些文件。

# 检查您的 Cloudflare 工作人员的结构

既然您已经准备好了一个简单的 worker，那么让我们浏览几个文件来理解项目的结构。

## 牧马人. toml

```
name *=* "cloudflare_worker_api"
main *=* "src/index.ts"
compatibility_date *=* "2022-10-28"
```

首先，我们有牧马人配置文件。每当您部署到 Cloudflare 时，都会用到它。目前这很简单，但是在后面的文章中，我们将介绍如何添加更多的配置来为您的工作人员添加更多的功能，比如存储或自定义域。

现在，这个名称是工作者的名称，它也将被用作您的工作者的子域。如果你改变它，一个新的工人将被部署在一个新的子域，你需要删除旧的。

其次，main 为您的 worker 指定了入口点。你的工人的入口点总是一个名为`fetch`的异步函数。

最后，`compatibility_date`将默认为您创建该员工的日期。这将用于确保 Cloudflare 的未来更改不会影响您的员工，至少在可能的情况下。

## src/索引. ts

我们将仔细研究的第二个文件是应用程序的入口点。目前，它看起来非常简单:

当您的 worker 被访问时，它将调用这里定义的异步获取方法。它被传递了三个参数:

*   `request`包含与一个 HTTP 请求相关的典型信息，比如方法(GET/POST 等)。)、被请求的 URL 以及任何头。
*   `env`包含[环境变量](https://developers.cloudflare.com/workers/platform/environment-variables/)，比如 secrets，在后面的文章中，通过自动为您创建的变量来访问存储，以便于访问。现在，我们必须定义空接口来让 TypeScript 满意。
*   `ctx`提供了一些[额外的功能](https://developers.cloudflare.com/workers/runtime-apis/fetch-event/#waituntil)，比如允许你向客户端返回一个响应，但是在响应发送后继续在 worker 中操作。

该函数必须返回一个响应，我们的工作人员目前只是返回经典的`Hello World!`文本。

现在我们已经了解了一个工人的结构，让我们看看它是如何工作的！你可以在你的项目的根目录下运行`npm start`，在你的浏览器中转到`[http://0.0.0.0:8787](http://0.0.0.0:8787/)`(或者在终端中按`b`，你应该会看到 Hello World！屏幕上的文字！

默认情况下，您的工作人员会响应对您的工作人员的 URL 的请求，因此转到`http://0.0.0.0:8787/foo`也会返回 Hello World。

值得注意的是:—当您运行`npm start`时，您仍然在攻击 Cloudflare 的网络，只是通过本地代理。这是真正测试你的代码的好方法，但是如果你没有互联网连接或者想减少对你的帐户的请求数量，你可以运行`wrangler dev --local`并使用输出提供的 URL。

接下来，我们将通过向 worker 添加一些简单的路由功能来介绍我们的简单 API。

# 创建一个简单的 API

此时，我们可以将我们的工作人员部署到 Cloudflare，并陶醉于看到 Hello World 回归的荣耀中。我认为您可以加快部署默认工作人员的速度，从帐户创建到部署，大约只需一分钟。

但是，我想向您展示一些更有用的东西，您可以在此基础上进行构建。在以后的文章中，我将进一步构建 API 并添加更多的通用功能，比如存储。

但这超出了我们的想象。现在，让我们创建一个非常简单的关于动物的 API。我们将使用 [itty-router，](https://github.com/kwhitley/itty-router)来为我们的 API 处理路由，所以让我们使用`npm install itty-router`安装 NPM。

接下来，我们需要将以下内容添加到 index.ts 文件的顶部:

代码被注释以解释发生了什么，但是本质上你可以用 itty-router 注册任意数量的路由。如果你想注册一个帖子请求，你可以选择`router.post`。

最后，我们更新 index.ts 文件底部的 fetch 方法来调用路由器:

```
export default {
   async fetch(
      request: Request,
      env: Env,
      ctx: ExecutionContext
   ): Promise<Response> {
      return await router.handle(request)
   },
};
```

所有这一切都是将请求传递给路由器，如果它找到与请求匹配的已配置路由，它将执行向该路由注册的逻辑。

你现在可以再次运行`npm start`，按下`b`，你会得到一个 404。如果您将 URL 更新为`[http://0.0.0.0:8787/animals](http://0.0.0.0:8787/animals)`，您应该会看到返回的动物的完整列表，如果您尝试使用`[http://0.0.0.0:8787/animals/1](http://0.0.0.0:8787/animals/1)`，您应该会看到返回的动物只有一头牛。

一个非常简单的 API，但仍然是一个 API！希望您能看到如何在此基础上构建一个实际的 API，但是在结束本文之前，在讨论存储等更高级的主题之前，让我们将 worker 部署到整个世界。

# 部署您的 Cloudflare 工作人员

在大多数项目中，这是您可能会抱怨部署应用程序或不得不掏出信用卡的部分。但不是用 Cloudflare。让我们使用以下步骤，毫无争议地完全免费部署:

1.  跑`npm run deploy`。这将启动牧马人的部署工作流程。第一次运行比以后的运行时间稍长。
2.  将会打开一个浏览器窗口。如果您尚未登录您的 Cloudflare 帐户，您需要登录并授权它。
3.  一旦授权，你会被要求选择一个子域。这将用于您的帐户，任何工人将被托管在`worker-name.sub-domain-your-pick-now.workers.dev`下。您可以在将来添加自己的自定义域，而不是使用 Cloudflare。
4.  您的员工现在将被上传到 Cloudflare。上传后，它将输出托管您的员工的 URL。您可以在以后添加更多的工人，他们将被添加到一个新的子域下(例如`worker-2.sub-domain-your-pick-now.workers.dev)`

如果这是你第一次注册子域，创建你的子域需要一点时间。你可以在[https://dash.cloudflare.com/](https://dash.cloudflare.com/)进入仪表板，点击左边的 Workers，然后是你的新工人的名字，你可能会看到一个小的加载圈，说明它正在初始化你的子域。

完成后，您可以在终端中访问新 API 的 URL 输出。要查看初始部署后部署有多快，您可以再次运行`npm run deploy`，它可能会在几秒钟内完成。您的工作人员可能需要一分钟的时间来传播 Cloudflare 的整个网络，因此，如果您没有立即看到您的更改，它可能仍会缓存您的旧更改。

# 摘要

这篇关于 Cloudflare workers 的文章到此结束，但请继续关注未来关于存储和自定义领域的文章。你可以在这里找到所有特性[的完整文档。你可以在 GitHub](https://developers.cloudflare.com/workers/) [这里](https://github.com/apeacock1991/cloudflare_worker_api/tree/simple_api)看到完整的工作源代码。

您可以在您的免费帐户中创建多达 30 个工人，每个工人的最大大小为 1MB。如果你的项目像病毒一样传播，并且你超过了 100，000 个请求/天的限制，那么每月只需 5 美元就可以升级到 1000 万个请求。你可以在这里看到完整的定价。

如果您有任何疑问或问题，请给我留言，我会尽力帮助您！

```
**I released a book all about diagramming!**[Click here](https://pragprog.com/titles/apdiag/creating-software-with-modern-diagramming-techniques/) to learn how to create diagrams to communicate relationships more directly and clearly than words ever can. Using only text-based markup, powered by Mermaid, create meaningful and attractive diagrams to document your domain, visualize user flows, reveal system architecture at any desired level, or refactor your code.
```