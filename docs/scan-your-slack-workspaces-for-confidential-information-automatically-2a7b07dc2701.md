# 自动扫描您的空闲工作区中的机密信息

> 原文：<https://betterprogramming.pub/scan-your-slack-workspaces-for-confidential-information-automatically-2a7b07dc2701>

## 在没有代码的情况下监视您的松弛工作区中的敏感信息

![](img/421285334eaeaa6050b6a5243306e895.png)

潘卡杰·帕特尔在 [Unsplash](https://unsplash.com/s/photos/slack?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我们都使用工具来协作。正确的基于文本的远程对话工具可以提高团队的工作效率。Slack 是团队协作中最常用的文本和音频/视频通话工具之一。

# 问题陈述

当一个团队或一个组织的许多成员使用 Slack 进行内部对话时，你不希望任何人泄露机密信息，无论是关于你的组织还是他们的个人生活。随着整个工作空间转向远程工作，筛选信息泄漏的松散渠道和可能有害的内容变得至关重要。敏感数据可能包括 API 密钥、私钥、恶意文件、出生日期等等。手动跟踪日志并删除所有恶意内容是不可能的。

# 解决方案(守夜人)

你需要一些东西来持续关注你松弛的工作空间。Slack Watchman 使用 Slack 提供的 API 来搜索你的 Slack 工作区中暴露的潜在敏感数据。

下面是详细的解释:

懒得看书？

## Slack Watchman 使用 Slack API 来查询您的工作区:

*   外部共享通道。
*   潜在的密码泄露。
*   AWS 键。
*   GCP 群岛。
*   谷歌 API 键。
*   松弛 API 键和 webhooks。
*   Twitter API 键。
*   脸书 API 密钥。
*   私钥。
*   秘密钥匙。
*   Paypal 布伦特里代币。
*   银行卡详情。
*   证书文件。
*   潜在的有趣/恶意文件(。docm，。xlsm，。zip 等。).
*   护照号码。
*   出生日期。
*   Paypal 布伦特里代币。
*   银行卡详情。

查询是完全动态的，你可以要求你的私人守夜人只寻找你认为可疑的东西，其余的将被忽略。所以，你有能力对最重要的事情保持警惕。

Slack Watchman 还可以输出您的工作区的所有频道、用户和管理员的列表，以便您可以在您的 Slack 工作区中审计任何不速之客。

您可以运行 Slack Watchman 来查找回溯到以下时间的结果:

*   24 小时
*   7 天
*   30 天
*   空前的

最好的方法是在安装时执行全时间扫描，然后每 24 小时或 7 天扫描一次。

结果存储在 CSV 文件中，您可以查看并对其采取措施。

# 使用和安装

要安装和运行 Slack Watchman，你必须有一个 Slack API OAuth 访问令牌，这个令牌可以通过创建一个新的 Slack 应用程序，直接到[https://api.slack.com/apps](https://api.slack.com/apps)来轻松获得

在您的 Slack 应用程序中添加以下**用户令牌范围**:

```
*channels:read
files:read
groups:read
im:read
links:read
mpim:read
remote_files:read
search:read
team:read
users:read
users:read.email*
```

## 提供令牌

在您的`HOME`目录中创建一个名为`slack_watchman.conf`的文件，并以以下格式提及您的 Slack 令牌:

```
[auth]
slack_token = xoxp-xxxxxxxxxx-...
```

或者，您可以将您的 slack 令牌保存在名为`SLACK_WATCHMAN_TOKEN`的环境变量中。Slack Watchman 将首先尝试从环境变量中获取令牌，然后从`slack_watchman.conf`文件中获取令牌。如果它未能获得 Slack 令牌，它将在控制台中给出一个错误，指出 Watchman 无法找到令牌。

## 通过 pip 安装

在[https://github.com/PaperMtn/slack-watchman/releases](https://github.com/PaperMtn/slack-watchman/releases)上也有源代码，但是推荐通过 pip 安装，这很简单:

```
pip install slack-watchman
```

通过运行以下命令，运行 Slack Watchman 来查找`everything`和`all-time`数据:

```
slack-watchman --timeframe a --all
```

Slack Watchman 将在全球范围内安装，它可以结合不同的参数进行更精细的搜索。

```
usage: slack-watchman [-h] --timeframe {d,w,m,a} [--version] [--all]
                      [--users] [--channels] [--pii] [--financial]
                      [--tokens] [--files] [--custom CUSTOM]

Monitoring your Slack workspaces for sensitive information

**optional arguments:**
 -h, --help            show this help message and exit
 --version             show program's version number and exit
 --all                 Find everything
 --users               Find all users, including admins
 --channels            Find all channels, including external shared
                       channels
 --pii                 Find personal data: Passwords, DOB, passport
                       details
 --financial           Find financial data: Card details, PayPal
                       Braintree tokens
 --tokens              Find tokens: Private keys, AWS, GCP, Google
                       API, Slack, Slack webhooks, Facebook,
                       Twitter, GitHub
 --files               Find files: Certificates, interesting
                       / malicious files
 --custom CUSTOM       Search for user defined custom search
                       queries. Provide path to .txt file containing
                       one search per line
**required arguments:**
 --timeframe {d,w,m,a} How far back to search: d = 24 hours w = 7
                       days, m = 30 days, a = all time
```

下面的命令将查找 AWS 密钥、GCP 密钥、令牌等。过去 30 天:

```
slack-watchman --timeframe m --tokens
```

您甚至可以输入自己的查询来搜索工作区中提到的敏感数据(例如机密项目名称)。

使用`--custom`命令行选项传递一个. txt 文件，每行一个搜索查询。所有包含自定义查询的帖子都将被返回。通用术语可能会在很长时间内返回大量结果。

# 开发商的信用

这个令人敬畏的开源工具是由来自英国的非常聪明的网络安全专家 Andrew Byford 构建的。对他表示感谢，并开始回购以备将来更新:[https://github.com/PaperMtn/slack-watchman](https://github.com/PaperMtn/slack-watchman)

随着远程工作成为一种常态，我们需要一个集中的环境供开发人员工作。我们需要一个能减少设置一切的痛苦的环境。看看 Github 的 Github 代码空间特性——它刚刚改变了编程游戏:

[](https://medium.com/better-programming/github-codespaces-code-anywhere-anytime-in-any-language-using-github-9f27c7a6b4a1) [## GitHub Codespaces:任何地方、任何语言的代码，全部来自您的浏览器

### GitHub 刚刚改变了编程游戏

medium.com](https://medium.com/better-programming/github-codespaces-code-anywhere-anytime-in-any-language-using-github-9f27c7a6b4a1)