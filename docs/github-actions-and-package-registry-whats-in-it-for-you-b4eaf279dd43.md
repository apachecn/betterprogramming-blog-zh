# 介绍 GitHub 的新特性——动作和包注册

> 原文：<https://betterprogramming.pub/github-actions-and-package-registry-whats-in-it-for-you-b4eaf279dd43>

## GitHub 向 CI/CD 和包管理领域的转移

![](img/9ee2b852c0408f95540a1fd215fbb8ba.png)

当微软在 2018 年夏天收购 GitHub 时，开发人员变得疯狂，GitLab 宣布，在宣布之前，有 5 万个新项目迁移到了它的平台上。

我不完全确定为什么开发者会觉得索伦的部落正在接管 GitHub，特别是因为纳德拉的微软并不完全是你父亲的微软，而且已经在支持开源软件和开发者社区方面取得了重大进展。

无论如何，在微软的支持下，GitHub 开发了两个有趣的新功能，今天同时宣布(很快)退出测试。

你觉得 GitHub 能变戏法多少？让我们看看提供什么。

# GitHub 操作

*动作*是 GitHub 向 CI/CD 领域的转移:

![](img/495dd2e9759fd7a256c7b6664b4d0838.png)

GitHub 操作欢迎页面

如果你以前曾与[詹金斯](https://jenkins.io/)、[特拉维斯 CI](https://travis-ci.org/) 、 [CloudBees](https://www.cloudbees.com/) 等类似的人合作过，GitHub Actions 会给你带来一个紧密集成到你的 GitHub 源代码库中的熟悉概念。

在这项新服务的初期，大量的功能和环境支持已经可用:

*   支持 Linux、macOS 和 Windows 中的构建容器。
*   跨不同环境进行测试时，并行工作流可以节省时间。
*   为大多数主流编程语言构建工作。
*   实时作业输出。
*   与您的源代码集成的工作流定义。
*   共享和使用实现经常需要的逻辑的社区工作流。

# 这对你有什么好处

*Actions* 对公共项目是免费的，符合 GitHub 的开源友好理念。闭源项目从每月 2000 分钟的免费构建时间开始，然后按每分钟收费。

CI/CD 市场在这一点上已经相当成熟，我们大多数人都已经理清了我们的 CI/CD 渠道。通过*动作*，GitHub 允许您减少构建和交付管道的移动部分，将所有东西集成在一个地方。

我可以看到，对于 CI 来说工作得很好，但是对于 CD 来说可能会有问题，考虑到部署目标可能不总是可以通过互联网访问的(当然，这不仅仅是 GitHub 的问题，而是任何基于云的 CI/CD 解决方案的共同困境)。

那么，我们是否应该将我们稳定、长期运行的 CI/CD 管道转移到 GitHub？

看情况。如果这是你正在做的一个新项目，因为作为软件开发人员，我们总是喜欢学习新的东西，我会尝试一下。不过，我可能不会将现有的、工作中的 CI/CD 管道迁移到具有高优先级的 GitHub。

# GitHub 包注册表

*包注册表*是 GitHub 向包管理领域的转移:

![](img/f153b49853e7f894d793eb57e75c8eee.png)

GitHub 包注册表欢迎屏幕

*Package Registry* 允许你推送你构建的包，这样你就可以私下或公开地分发它。

它目前支持一些最常用的包和容器库，如 [npm](https://www.npmjs.com/) 、 [Docker](https://www.docker.com/) 、 [Maven](https://maven.apache.org/) 、 [NuGet](https://www.nuget.org/) 和 [RubyGems](https://rubygems.org/) 。

*包注册表*的一些显著特性包括 *:*

*   您的所有安全凭证(代码和包)都在一个地方。
*   通过透明 CDN 的边缘缓存加速下载。
*   API 和 webhooks 轻松集成到您的工作流中。
*   集成到 GitHub 熟悉的 UI 中的包内容、历史和下载统计。

# 这对你有什么好处

*包注册*对公共项目是免费的，而对私人项目使用的存储和带宽是收费的。那么你应该把你的包裹和集装箱转移到*包裹登记处*吗？

嗯，再说一次，这要看情况。

首先，私人项目的定价政策与目前广泛使用的政策略有不同。

例如，在 Docker Hub 中，您需要为每个私有存储库付费，但是不需要为存储或带宽支付额外费用。这同样适用于 NPM，在那里你按用户数量收费，但也不收取额外费用。

如果你已经把你的包推到一个标准的仓库，当切换到*包注册表*时，另一个小麻烦是你的用户将不得不明确地把他们的包管理器客户端切换到 GitHub。

这不应该阻止你使用*包注册表*，尽管活动部件越少越好。

# 结论

GitHub 通过*动作*移动到 CI/CD 空间，通过*包注册表*移动到包管理和分发空间。

这两个服务都是经过深思熟虑的，并与 GitHub 的其他功能紧密集成，但是否应该移植您的项目来使用它们，实际上是一个特定于项目的决定。