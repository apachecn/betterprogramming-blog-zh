# 使用 VOXA 构建跨平台语音应用程序—第 1 部分

> 原文：<https://betterprogramming.pub/building-cross-platform-voice-apps-with-voxa-part-1-547f50675fa6>

## 数学测验应用程序入门

![](img/ffa4ef44222181506371c4c9af11b8a1.png)

图片经由[www.supercoloring.com](http://www.supercoloring.com/silhouettes/evolution-of-man)并根据技术参考进行了调整

这是五个系列的第一部分，涵盖了为什么为你的下一个语音应用程序使用跨平台框架是一个好主意。我还将解释如何利用 VOXA 的 API 为 Alexa 和 Google Assistant 创建一个快速的跨平台游戏。

自第一个语音平台亚马逊 Alexa 的第一个开发工具包发布以来，已经过去了四年。自那以后，该平台发生了巨大的变化，其他竞争对手也加入了市场——谷歌助手、Cortana、Siri、三星 Bixby 等。

创建这些平台的公司也为开发人员构建了为他们的平台创建应用程序的框架。开发人员面临的第一个难题是:如果我想在一个以上的平台上开发我的技能:我应该在两个或三个不同的框架中编写代码吗？

让我告诉你我对自己的回答:*奥克塔维奥，不要！不要那样做！取而代之的是，使用一个涵盖部分或全部的框架。*

这就是我为 Alexa 开发技能和为 Google 开发动作所做的工作。我选择了 [VOXA](http://voxa.ai/) ，一个基于 Node.js 的框架，利用状态机和 MVC 原则为 Alexa 和 Google Assistant 开发语音应用。另外，它还支持为 Telegram 和 Facebook Messenger 创建应用程序。

如果你是一名开始钻研 Alexa 和 Google Assistant 语音应用程序的开发人员，或者是一名没有开发技能的用户体验设计师，或者是一名使用 Alexa SDK 或 Google Assistant SDK 等其他框架创建技能/动作的经验丰富的开发人员，你会发现 VOXA 是一个与团队协作的非凡工具。

因此，我想向你展示如何使用 VOXA 轻松构建 Alexa 和 Google Assistant 的技能。该框架还有一个很好的命令行界面:VOXA-CLI，它有一套有用的工具来创建 VOXA 项目、下载话语和响应以及许多其他东西。如果您与其他开发人员和/或语音用户界面(VUI)设计人员一起工作，甚至与项目经理一起工作，所有人都可以为应用程序的 VUI 部分进行协作。

这是非常有用的，例如，当一个 VUI 设计师想要为你的应用程序添加、修改或删除话语，或者想要修改一个响应。有很多方法可以从 VUI 设计师那里得到这些改变。

例如:

*   在你的项目中有一个单独的文件，让 VUI 设计者对文件进行修改，然后把它放回项目中。
*   将代码上传到 Git 存储库，并让 VUI 设计人员提交一个包含文件更改的 Pull 请求。

由于这两种方法是可选的，VUI 的设计师可能不知道 Git。或者他们可以在文件中出错。使用 VOXA，你可以创建一个电子表格，并让你的 VUI 设计师、营销人员、项目经理或任何负责 VUI 部分的人修改电子表格，只需在你的终端上发出一个命令，你就可以下载他们的更改，以创建应用程序的响应文件、Alexa 的交互模型和 Google Assistant 的 Dialogflow 代理。

这是一个用例。这个游戏叫做*快速数学*:用户需要尽可能快地做一个数学计算——如果他们回答正确，他们将获得如下分数:

用户在 1- 2 秒内回答正确:10 分

用户在 3 - 4 秒内回答正确:8 分

用户在 5 - 6 秒内回答正确:4 分

用户在 6 秒钟内回答正确:1 分

有趣的部分:如果用户回答错误，将从他们的分数中扣除 2 分。如果他们得了 0 分，就没有扣分。

很简单吧？嗯，我们要把它变得更复杂一点。我们将使用 VOXA 获取用户的个人资料信息:

*   获取用户资料:我们需要用户的名字来问候他们的名字，当他们回答正确。我们需要用户的电子邮件将信息保存在我们的数据库中。我们将设置 Alexa 的帐户链接和谷歌的登录来获取这些信息。

有趣的部分来了:我们将为我们的游戏添加一个竞赛功能。得分最高的用户将成为每月竞赛的获胜者。我们将展示前 10 名玩家的排行榜。正如你可能猜到的，我们将为此添加一个订阅。用户将被收取每月 0.99 美元，以继续参与竞争。

这听起来像货币化吗？是的，它是！

我们将使用 Alexa 的 [ISP](https://developer.amazon.com/docs/in-skill-purchase/isp-overview.html) 和谷歌助手的[数码商品](https://developers.google.com/actions/transactions/digital/dev-guide-digital)来实现这个功能。为了使它更有趣，我们将询问用户是否允许获取他们的位置，这样我们就可以在排行榜上显示他们来自哪里。

本系列接下来的四篇文章将介绍如何使用 VOXA 建立项目。以下是详细情况:

第 2 部分:设置 VOXA、Alexa 的帐户链接以及 Google 的登录、意图和话语。

[第三部分:开发游戏的逻辑，获取用户的个人资料，并将用户的分数存储在 AWS DynamoDB 中。](https://medium.com/better-programming/building-cross-platform-voice-apps-with-voxa-part-3-e738cd991fde)

[第四部分:添加 Alexa 的 ISP 和 Google Assistant 的数字商品，用于比赛订阅和用户的位置许可。](https://medium.com/better-programming/building-cross-platform-voice-apps-with-voxa-part-4-f7de737b0cd5)

[第 5 部分:添加游戏排行榜和单元测试。](https://medium.com/better-programming/building-cross-platform-voice-apps-with-voxa-part-5-1f0aff32185)

# 最后的话

这是一系列文章中的第一篇，我将向您展示使用 VOXA 创建语音应用程序有多简单，以及我们如何利用它的 API 来创建更复杂的场景。

感谢阅读！请务必查看该系列的其余部分。

不要往海里扔垃圾！