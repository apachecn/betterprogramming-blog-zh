# 我如何将每日电码挑战同步到 Todoist

> 原文：<https://betterprogramming.pub/how-i-sync-daily-leetcoding-challenge-to-todoist-e50784bbcc8>

## 一天一码，远离失业

![](img/3fbee5dae888083d5c9584050d3a3ad8.png)

照片由[内容小精灵](https://unsplash.com/@contentpixie?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你曾经在找软件开发人员的工作，你会遇到所谓的 LeetCode 风格的面试。

尽管事实上我们大多数人在实际工作中并不需要[倒置二叉树](https://leetcode.com/problems/invert-binary-tree/)，但在谷歌和微软这样的大型科技公司，大多数编码/技术面试都是这样进行的。是的，即使是像[独角兽](https://www.cbinsights.com/research-unicorn-companies)(除了 Stripe，因为他们很酷)和初创公司。

在这篇文章中，我将讲述我是如何用 [Cloudflare Worker](https://workers.cloudflare.com/) 免费构建和部署一个非常简单的 JavaScript 应用程序的思考过程。如果你只是想自己部署和使用这个应用程序，请点击这里查看资源库。

## TL；速度三角形定位法(dead reckoning)

*   如何使用 LeetCode API 获取每日 LeetCoding 质询问题
*   使用 Todoist API 创建 Todoist 任务
*   使用 cloud flare Worker scheduled event API 安排我们的应用程序每天运行
*   如何使用 Miniflare 在本地测试 Cloudflare Worker Cron 触发器

# 问题陈述

从一年前开始，我就一直试图把解决每日 LeetCoding 挑战(我仍在为此奋斗)变成一种习惯。当我使用 [Todoist](https://todoist.com/) 作为我选择的主要生产力工具时，我的日常任务如下:

![](img/c8c0390ec69b675706a9c2f4787d8ced.png)

我的习惯性任务

作为一个懒惰的人，每次我想练习的时候都要检查 leetcode.com，这太麻烦了。所以我想，为什么不把每天的 LeetCoding 挑战同步到我的 Todoist 上呢？

# 要求

让我们从定义我希望应用程序做什么开始:

1.  获取每日电子编码挑战问题
2.  在我的 Todoist 帐户上创建新任务的能力
3.  准时同步新的每日电子编码挑战问题
4.  该应用程序必须每天免费按时同步

开始吧！

# LeetCode API

像任何正常人一样，我做的第一件事是做一些研究。我说的研究是指我开始用谷歌搜索信息。

我做的第一件事是立即谷歌“ *leetcode API* ”，寻找官方 API 文档。

## 官方 API 文档

令我惊讶的是，没有任何官方的 LeetCode API 文档可用。虽然 GitHub 上有几个非官方的 LeetCode API 库，但出于可靠性考虑(维护不佳、过时等)，我宁愿不使用任何非官方的 API。).

## 良好的旧开发工具检查

我立即想到的第二件事是检查在访问[https://leetcode.com/problemset/all/](https://leetcode.com/problemset/all/)网站时发出的网络请求。

有了这个，我就能够计算出查询每日 LeetCoding 挑战所调用的确切 API 完成了。

![](img/a2587dd73530f525d1e86391f410799e.png)

因为 LeetCode 使用的是 [GraphQL](https://graphql.org/) ，所以您需要检查“Payload”选项卡来查看 GraphQL 主体

下面是 GraphQL 请求体:

您可以使用下面的`curl`命令亲自尝试一下:

## 密码

说得够多了，让我们开始写一些代码来完成我们所经历的事情:

任务“获取每日电子密码质询问题”-已选中。

# Todoist API

就像我们在上一步中所做的那样，我能够找到 Todoist 的官方 API 文档。通常，我总是在 API 文档中寻找的第一个部分是[授权](https://developer.todoist.com/rest/v1/#authorization)部分，尤其是当您想要在应用上执行创建/更新/删除操作时。

简而言之，对于 Todoist 来说，授权非常简单:

1.  [获取您的 API 令牌](https://todoist.com/app/settings/integrations)
2.  每当发出请求时，将`Authorization: Bearer xxx-your-todoist-api-token-xxx`附加到 HTTP 请求头

下面是一个在 Todoist 上给[创建新任务的`curl`命令的例子:](https://developer.todoist.com/rest/v1/#create-a-new-task)

用您自己的 API 令牌替换它

## 密码

编写一个能完成我们所说的功能的函数相对来说比较容易，它看起来像这样:

正文内容采用 Markdown 语法

任务“在我的 Todoist 帐户上创建新任务”——完成。

# 云闪工人

我们还有最后一项任务——每天免费运行/自动化上述 2 项任务。

我首先想到的是克朗乔布。于是，我立刻开始在网上寻找免费的解决方案。在花了几个小时做了一些功课后，我遇到了 Cloudflare Worker，我想尝试一下。

## 它运行在 V8 JavaScript 上，而不是 Node.js 上

这可能是对 Cloudflare Worker 最常见的误解[之一。因为工作人员的环境不在 Node.js 中，所以在 Node.js 上运行的许多包(例如`npm install some-node-package`)根本无法工作。](https://blog.cloudflare.com/introducing-cloudflare-workers/)

> *提示:在这里* *查看支持的包和库* [*。*](https://workers.cloudflare.com/works)

幸运的是，我们只需要使用 JavaScript 内置的`[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)` API。

## 更多代码

启动 Cloudflare Worker 项目非常简单([参考](https://developers.cloudflare.com/workers/get-started/quickstarts))，基本上:

1.  使用`npm install -g @cloudflare/wrangler`安装牧马人 CLI
2.  运行`wrangler generate <your-project-name> <[worker-template](https://developers.cloudflare.com/workers/get-started/quickstarts#templates)>`
3.  切入点是`addEventListener`功能。对于我们的用例，我们将使用 [ScheduledEvent](https://developers.cloudflare.com/workers/runtime-apis/scheduled-event) API，我们只需将事件从`"fetch"`更改为`"scheduled"`

让我们把所有的东西缝合在一起:

接下来，我们只需要修改`wrangler.toml`如下:

每日 LeetCoding 挑战赛将于世界协调时 00:00 刷新

有了上面的设置，工作人员将每天在协调世界时[00:01](https://crontab.guru/#1_0_*_*_*)跑步，并将每天的 LeetCoding Challenge 同步到您的 Todoist。

仅此而已！继续测试。

# 如何在本地测试 Cloudflare 工作人员

为了在本地测试 Cron 触发器，我们需要安装 [Miniflare](https://miniflare.dev/cli.html) CLI。安装后，您可以使用以下工具运行您的工作进程:

```
# At terminal 1
miniflare# At terminal 2
curl "[http://localhost:8787/.mf/scheduled](http://localhost:8787/.mf/scheduled)"
```

如果你看到一个新的任务被创建在你的待办事项上，你已经完成了！

# 部署

没有主持过的副业是做不完的。

要立即自行部署应用程序，请检查[项目存储库](https://github.com/ngshiheng/todoleet)，并使用“[与 Workers 一起部署”按钮](https://deploy.workers.cloudflare.com/?url=https://github.com/ngshiheng/todoleet)。如果您对技术感兴趣:

1.  创建 Cloudflare 帐户。
2.  使用`wrangler secret put TODOIST_API_TOKEN`添加`TODOIST_API_TOKEN`。你可以在`Cloudflare Worker` → `Settings` → `Variables`下找到新增的秘密。你可以在这里获得你的 Todoist API 令牌[。](https://todoist.com/app/settings/integrations)
3.  可选:这仅在[牧马人动作](https://github.com/marketplace/actions/deploy-to-cloudflare-workers-with-wrangler)时需要。将`CF_API_TOKEN`添加到您的 GitHub 库秘密中。您可以使用`Edit Cloudflare Workers`模板从[https://dash.cloudflare.com/profile/api-tokens](https://dash.cloudflare.com/profile/api-tokens)创建您的 API 令牌。
4.  最后，要向您的 Cloudflare Worker 发布任何新的更改，请运行`wrangler publish`

我们终于完成了！

# 结束语

最后，我们还可以做更多的事情，例如:

*   处理前几天未回答的问题
*   让用户可以配置/定制应用程序
*   向我们的任务添加标签
*   允许用户创建一个随机的 LeetCode 问题作为基于问题标签的任务

我打算改天再讨论这些特性。

虽然有很多人讨厌对面试进行编码，但我个人是这样看的——通过学习一些脑筋急转弯，你可能会得到更多的报酬，为什么不呢？这确实是一个视角的问题。如果你碰巧喜欢这样做，那就更好了。

就我个人而言，我觉得做 LeetCode 题没什么乐趣。相反，我解决 LeetCode 问题就像在健身房举重一样。虽然我不喜欢举重，但我喜欢从中获益。

今天到此为止。让我知道你正在用 Cloudflare Worker 构建什么！

```
**Want to Connect With the Author?**This article was originally published at [jerrynsh.com](https://jerrynsh.com/how-i-sync-daily-leetcoding-challenge-to-todoist/)
```