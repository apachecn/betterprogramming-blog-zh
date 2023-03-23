# 以每月不到 5 美元的价格在云中托管您的应用程序

> 原文：<https://betterprogramming.pub/host-your-app-in-the-cloud-for-less-than-5-a-month-cba15cdb6cd8>

## 这是一种在云中部署和托管 NodeJs、Python、Java 或 Ruby 应用程序的简单而廉价的方式，几乎不需要开发运维技能

![](img/0c130c0808808049da0a6d5312e27d35.png)

作者制作的图像

我找不到关于在云中托管自己的应用程序的完整指南。因此，在花了一些时间研究并亲自动手之后，我认为在这里收集所有这些信息对未来的我会有所帮助，当然，对所有更好的编程读者也是如此。

我使用 [Hetzner Cloud](https://www.hetzner.com/cloud) 提供商来托管我的[项目](https://github.com/vladmykol/mando-chatbot)。如果你想知道为什么，看看我在[之前的文章](/aws-vs-digital-ocean-vs-hetzner-cloud-which-has-the-best-value-for-money-bd9cb3c06dee)，在那里我比较了其他流行的选择。

# 1.登录赫茨纳并获得 Linux VPC

您需要先注册并指定您的付款方式。当我以前自己做的时候，我被要求提供我的 ID 和 20 美元的信用来激活我的帐户。不用担心；它将用于您将来与海兹纳的发票。注册流程非常简单明了，但是如果您需要一步一步的指导，请点击这里:

[](https://www.banjocode.com/post/hosting/setup-server-hetzner/) [## 如何在 Hetzner 上设置 Linux (Ubuntu)服务器

### 远程 linux 服务器可能非常有用。Hetzner 提供便宜可靠且易于维护的服务器。这个…

www.banjocode.com](https://www.banjocode.com/post/hosting/setup-server-hetzner/) 

*不要错过 SSH 密钥设置。否则，您将无法连接到您的服务器。即使你做了，删除它，并再次启动一个新的服务器。在这里只需要几分钟。

# 2.安装 Dokku

Dokku 是一个开源的 PaaS，可以自动创建 Docker 容器、日志、数据库管理、Git 部署等。这些都是您的应用程序在远程服务器上运行所需的东西。Dokku 在零成本自动化许多 DevOps 任务方面表现出色。

来自赫茨纳的新服务器只不过是一台空的远程机器(VPC)。您将需要使用 SSH 连接到您的服务器，并从现在开始做所有必要的设置。逐一运行以下命令来安装 Dokku:

```
ssh root@<ip-address>
```

> 其中<ip-address>是您在 Hetzner Cloud dashboard 中新创建的服务器的 IPv4 地址。</ip-address>

```
wget [https://raw.githubusercontent.com/dokku/dokku/v0.27.5/bootstrap.sh](https://raw.githubusercontent.com/dokku/dokku/v0.27.5/bootstrap.sh)
```

> 其中 v0.27.5 是 Dokku 的最新版本。当你读到这篇文章时，那里可能有一个更新的版本，所以检查一下这个并使用最新的版本。

```
sudo DOKKU_TAG=v0.27.5 bash bootstrap.sh
```

现在，您可以将您的“互联网地址”(域名)分配给未来的应用程序。如果您没有域名，可以稍后配置。我现在将使用 IP 地址。

```
dokku domains:set-global <ip-address or domain-name>
```

> 其中<ip-address>是您在 Hetzner Cloud dashboard 中新创建的服务器的 IPv4 地址。</ip-address>

*如果您在上述安装过程中遇到错误，请尝试遵循另一个指南，特别是来自[这一步](https://medium.com/@auth0/hosting-applications-using-digitalocean-and-dokku-48ab2af1f885#:~:text=Inside%20the%20terminal%3A)，因为它可能会提供更详细的解释和示例。

# 3.创建您的应用基础设施

如果您使用下列编程语言之一:

*   红宝石
*   节点. js
*   Clojure
*   计算机编程语言
*   Java 语言(一种计算机语言，尤用于创建网站)
*   斯卡拉
*   服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

那么你是幸运的，因为 Dokku 用 Buildpacks 自动检测，并在这种情况下自动完成许多任务。(构建、启动、联网和运行状况检查)。

使用 SSH 连接到 Hetzner 云服务器时运行以下命令(与之前所有命令的窗口相同):

```
dokku apps:create <your-supper-app-name>
dokku mongo:create <your-db-name>
dokku mongo:link <your-db-name> <your-supper-app-name>
```

在上面的例子中，我使用 MongoDB，但是你可以选择任何其他的数据库引擎，比如 MySQL 或 Postgress，只要它们在 [Dokku 插件](https://dokku.com/docs/community/plugins/)中可用。

最棒的是，现在只要你有足够的服务器容量，你就可以创建和托管任意多的应用程序。所有这些都是可能的，因为多库正在引擎盖下使用码头集装箱。

# 4.释放它

发布您的应用程序和部署任何未来更新的最简单方法是将您的源代码保存在 [GitHub](https://github.com/) 上，并创建您的个人[访问令牌](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-token)。所有这些都是免费的，即使是私有库(不是开源代码)。

```
dokku git:allow-host github.com
dokku git:auth github.com <your-github-username> <your-github-access-token>
dokku git:sync --build <your-supper-app-name> <your-github-repository-url>
```

Dokku 可以在每次提交时运行自动部署，但是我不喜欢这样做，并且可以使用`dokku git:sync`命令对在哪里部署我的更改进行更多的控制。如果一切正常，您将能够在此处看到您的应用程序日志:

```
dokku logs <your-supper-app-name> -t
```

查看 my [GitHub repo](https://github.com/vladmykol/mando-chatbot) 获取 Java 应用程序部署过程的完整示例。

# 最后的想法

这就是你如何用一个便宜的起始标签在云中托管多个应用程序，并在未来使用运行 Dokku 的 Hetzner 服务器轻松更新和扩展它。

尽管这里没有涉及到，而且它是可选的，我还是鼓励您购买一个便宜的域名，并与 Dokku 一起使用。然后，将来您可以更加透明地访问您部署的应用程序，并与任何人共享它们。

我试图让这个指南尽可能简单，但我可能错过了一些东西。

如果你觉得可以，我很乐意在评论里回答你的问题。

希望在我以后的文章中，我能够报道更多关于在云中运行我自己的应用程序的令人兴奋的事情。