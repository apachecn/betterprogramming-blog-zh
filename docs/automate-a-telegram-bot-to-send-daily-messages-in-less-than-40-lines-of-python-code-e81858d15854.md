# 自动化电报机器人，用不到 40 行 Python 代码发送日常消息

> 原文：<https://betterprogramming.pub/automate-a-telegram-bot-to-send-daily-messages-in-less-than-40-lines-of-python-code-e81858d15854>

## 创建一个电报机器人，安排它发送每日消息，并部署在 AWS Lambda 中

![](img/7ff944ed995cdfab3eaf3bdde34e2749.png)

由 [timJ](https://unsplash.com/@the_roaming_platypus?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

几天前，我发现自己再次从一个 API 中检查一些随机信息。尽管该 API 允许过滤并有一个邮件系统，但出于某种原因，它不允许将两者结合起来。

要么我每天给我发一封包含所有信息的邮件，要么我每天手动过滤 API。不太好。

所以，遵循阿尔·斯威加特的哲学([将枯燥的东西自动化！我决定自动完成这项任务。](https://automatetheboringstuff.com/)

显然，这是我个人的用例。你可能想每天发布到一个[电报](https://telegram.org/faq_channels)频道，或者每周发送一条信息，或者每五分钟收到一条关于一些事件结果的提醒；所有这些例子都适合这里。

所以，总结一下:我们要做什么？

*   创建一个电报机器人。
*   安排它发送每日消息。
*   将其部署在 [AWS Lambda](https://aws.amazon.com/lambda/) 中。

我们需要什么？

*   电报账户。
*   [Python](https://www.python.org/) 3.6。
*   [Node.js](https://nodejs.org/) 。
*   具有管理员权限的 AWS 帐户。

请注意，AWS Lambda 在一定限度内是自由的，但是如果您想要发送大量消息，请注意配额。

# 创建机器人

待办事项清单上的第一件事是创建一个机器人。我遵照[的官方指示](https://core.telegram.org/bots#6-botfather)发来电报:

*   在 Telegram 中搜索用户@BotFather。
*   使用命令`\newbot`为你的机器人选择一个名字和用户名。
*   获取令牌并将其存储在安全的地方，您很快就会需要它。

现在机器人已经准备好了，是时候开始编码了。

# 准备部署

有几种部署到 Lambda 的方法。跟随来自 - schedule: cron(00 21 * * ? *)

根据需要更改字段`org`和`app`。

之后，我们告诉 AWS 我们想要的运行时类型，并从我们自己的环境中传播电报令牌，这样我们就不必部署它了。

最后，我们将 cron 定义为每天在 UTC 时间 21:00 运行该函数。当然，cron 允许其他选择；每小时或者，我不知道，每个月的第一个星期一，检查一下文档。

# 收尾工作

我们已经有了我们需要的一切。

一切？嗯，几乎所有的事情。我们需要获得 AWS 凭证，并在部署之前将它们和令牌一起设置为环境变量。获取凭证相当容易:

从您的 AWS 控制台:

*   进入*我的安全凭证* — *用户* — *添加用户。*
*   选择用户名并选择*程序化访问。*
*   下一页:选择*直接附加已有策略* — *管理员访问。*
*   复制*访问密钥 ID* 和*秘密访问密钥*并保存。

就是这样。现在，让我们导出 AWS 凭证和电报令牌。打开终端，写下:

```
$ export AWS_ACCESS_KEY_ID=[your key goes here]
$ export AWS_SECRET_ACCESS_KEY=[your key goes here]
$ export TELEGRAM_TOKEN=[your token goes here]
```

在本地安装必要的软件包:

```
$ pip3 install -r requirements.txt -t .
```

最后，将一切部署到 AWS:

```
$ serverless deploy
```

我们完了！机器人会在世界协调时每天 21:00 给我们发消息。你可以在 GitHub 里查看[的代码。](https://github.com/ruromgar/scheduled_tg_bot)

# 参考

*   无服务器团队， *AWS Python Scheduled Cron 示例*:[https://github . com/server less/examples/tree/master/AWS-Python-Scheduled-Cron](https://github.com/serverless/examples/tree/master/aws-python-scheduled-cron)