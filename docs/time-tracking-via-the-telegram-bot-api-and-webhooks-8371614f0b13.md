# 通过电报机器人 API 和 Webhooks 进行时间跟踪

> 原文：<https://betterprogramming.pub/time-tracking-via-the-telegram-bot-api-and-webhooks-8371614f0b13>

## 我对理想的时间跟踪工具的搜索是如何以一个令人惊讶的结果结束的

![](img/9241f257b9d26f831f3a4b813959812f.png)

图片来自[饲养的](https://unsplash.com/@bradneathery)的 [Unsplash](https://unsplash.com/photos/nPy0X4xew60)

在本文中，我想介绍一种使用 Telegram 的 Bot API 和 Webhooks 来跟踪时间的方法。我将指导您完成安装、配置和部署。但首先，让我们来看看这个想法是如何出现在我脑海中的。如果你只对技术部分感兴趣，跳到下一部分。

# 介绍

在我们的一次晚间谈话中，我妻子说她需要为过去三个月的工作写一份小时报告，并试图找出她工作的确切时间。她没有任何工具来帮助她跟踪自己的时间，所以在旧笔记和聊天中找到所有这些东西是一件相当痛苦的事情。然后她告诉我，她同意她的老板建立一个 WhatsApp 群，她在开始工作和停止工作时写下一条信息，根据这些信息，很容易填写各自的报告。

我想:真是个好主意。你几乎总是带着你的手机，只写“开始”或“结束”什么的没什么大不了的。我对此印象颇深，并思考了这种方法。在过去的几个月里，我花了很多时间来寻找好的工具来跟踪我的时间，但总是有这样一个问题:需要很容易输入我现在正在做一项任务或完成一项任务，并且它必须可以从我所有的设备上完成。

因此，我尝试了一些可以在我的 Mac、Linux 机器和手机上运行的时间跟踪应用程序，但没有什么真正让我吃惊。回到我妻子如何追踪时间的想法，我认为在 messenger 中写下我何时开始什么任务以及何时完成它是一个好主意。在对机器人和 Webhooks 做了一些研究后，我基于 telegram 构建了一些工具。

我的想法是创建一个电报机器人，并为特定格式的消息实现 Webhooks，以 CSV 格式保存时间戳和任务描述。让我们来看看它是如何工作的。

# 设置项目

起初，我开始搜索现有的使用 Telegram Webhooks 的库或示例，并在 GitHub 上找到了这个:[节点-telegram-bot-api](https://github.com/yagop/node-telegram-bot-api) 。它看起来很容易使用，我是 Node 的粉丝，所以我决定尝试一下。因为它提供了一个库，所以我需要设置一个节点应用程序。比起 JavaScript，我更喜欢 TypeScript，所以我相应地设置了我的项目。

有一个很酷的工具叫做 typescript-starter，你可以通过`npx typescript-starter`把它作为 [npm 可执行文件](https://github.com/bitjson/typescript-starter)来运行。它会问你一些问题，比如，你是想建一个库还是一个应用程序，对我来说是后者。在通过在 typescript-starter 的设置对话框中保留默认值来设置项目之后，我创建了一个启动配置，用于在 VS 代码中进行调试。经过一些试验后，我的最终启动配置如下所示:

代码的主要部分进入`src/main/index.ts`。为了配置发送到电报机器人的消息的 Webhooks，node-telegram-bot-api 提供了一个很好的模板，我对它做了一些修改。首先，我安装了所需的软件包:
-`npm i --save node-telegram-bot-api`-
-`npm i --save @types/node-telegram-bot-api`

## 电报机器人代码

然后，我将示例代码中的导入从`require`改为`import`(以获得导入代码的类型安全)并添加我的令牌。代码看起来像这样，并准备好进行测试:

使用`on`和`onText`钩子，调用每个传入消息(on)或匹配特定(`onText`)正则表达式的消息的函数。在这些函数中，示例代码通过将消息发送回相应的聊天来回答。在我看来，这个库提供的抽象层次非常高。所以我准备测试这个例子。

一开始我是通过去 telegram(或者 web telegram)，打开聊天到 [Botfather](https://core.telegram.org/bots#6-botfather) (按照文档中的 t.me 链接)，写`/newbot`，按照要求给它起个名字和用户名，来创建一个 bot。僵尸父亲打印了令牌，需要在代码开头的令牌常量中设置。之后，我通过之前添加的 VS 代码中的 launch 命令运行 telegram API bot。

在通过点击我从机器人父亲那里收到的链接并点击右上角的开始来打开一个新的聊天后，我能够通过向机器人发送`/echo Hello`来测试 Webhooks。它应该回复 hello(通过`onText`函数)并发送另一个文本“已收到您的消息”

太好了！现在让我们给示例代码添加更多的功能。

# 添加用于时间跟踪的处理程序和代码

如前所述，我的目标是以 CSV 格式存储我的时间跟踪，以便稍后在 Excel 中进行编辑。在 Node 中处理 CSV 文件非常容易。尽管许多库都在做这项工作，我还是决定从头开始写，只是为了好玩。对于我的原型，我希望我的机器人有以下命令:
- `/work $message` - >开始任务`$message`-`/done`->完成最后开始的任务
- `/state` - >显示最后任务的状态(开始或完成)
- `/print` - >将整个 CSV 写入聊天

为了读写 CSV 条目，我实现了以下两个函数:

函数`getEntries`将 CSV 内容作为 JS 对象返回，`writeFile`函数将 JS 对象存储到 CSV 文件中。所以，没有必要使用库。通过这两个函数，我实现了上面提到的四个处理程序:

`work`命令添加一个带有`startDate`的条目，并将相应的文本添加到`/work`。`/done`命令检查文件中是否至少有一个条目，并设置`endDate`和持续时间。`/state`和`/print`处理程序的工作方式类似，做我上面介绍的事情。就是这样！

代码不多，但是使用这个工具感觉很酷。我在调试模式下运行它，以测试一切是否如预期的那样工作。太好了。现在让我们开始最后一项任务:部署。

# 电报时间跟踪器的部署

我添加了一个多阶段构建的 Dockerfile，以便在任何主机上运行它。您还可以将令牌作为 env 变量或类似变量进行传递，以便进行部署。在服务器上运行它的最简单的方法(使用 docker 设置)是将您的 repo 推送到服务器，然后构建并运行容器(在将令牌更改为 bot 令牌之后):

*   `docker build -t telegram-timetracker:latest`
*   `docker run -ti --rm telegram-timetracker:latest`

可以通过 env 变量来设置令牌，以避免编辑代码，但这只是一个小的优化。整个回购可以在这里找到[。我非常喜欢这种跟踪任务的方式。](https://github.com/martenwallewein/telegram-timetracker)

请随时分享任何反馈。我很乐意把它加到这篇文章里。