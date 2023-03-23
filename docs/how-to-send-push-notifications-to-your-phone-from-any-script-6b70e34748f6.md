# 如何从任何脚本向您的手机发送推送通知

> 原文：<https://betterprogramming.pub/how-to-send-push-notifications-to-your-phone-from-any-script-6b70e34748f6>

## 任务完成时获得通知

![](img/5d29e473d3ac7dae5bf72f97b554a887.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

作为程序员，我们经常需要启动长时间运行的任务和脚本。这可能包括:

*   传输或复制大文件
*   处理大数据集
*   冗长的编译过程

对于任何一种耗时的脚本，知道脚本何时结束是很方便的，如果不是必要的话。我们还想知道这个过程是成功还是失败。

虽然有些软件内置了这类通知，但大多数命令行工具没有。此外，理想情况下，我们希望无论身在何处都能获得通知，而不仅仅是在运行脚本的设备上。

# 使用 IFTTT

![](img/0496f7dc4d07c9549453143d56d5dd2a.png)

图片由作者提供

通过 web 服务 [IFTTT](https://ifttt.com) ，可以轻松实现向任何移动设备发送推送通知。这可用于将发送到给定 URL 的 web 请求连接到安装在移动设备上的配套应用程序。

以下是实现这一目标的步骤:

1.  访问[http://ifttt.com](http://ifttt.com)并注册账户。
2.  点击您的账户图标(右上角)，然后点击创建**。**
3.  点击 If+图标，在搜索框中输入“Webhooks”。
4.  选择 Webhooks →接收 Web 请求，并为您的事件名称键入“notify”。
5.  接下来，单击+图标，并在搜索框中键入“通知”。
6.  选择“从 IFTTT 应用程序发送通知”
7.  在消息框中键入以下文本:

```
Notification: {{Value1}}
```

8.点击“创建操作”，然后为您的操作命名，例如“推送通知”然后点击完成。

恭喜你！您现在已经创建了一个 IFTTT 操作。

# 测试通知

要测试通知，您需要在移动设备上下载 IFTTT 应用程序，并使用您的帐户登录。它会提示您是否希望接收通知—选择“是”

假设应用程序已正确安装在您的设备上，我们现在可以返回网络浏览器，找到我们的小程序的 URL。

为此，请访问[https://ifttt.com](https://ifttt.com)，然后:

1.  点击您的帐户图标(右上角)，然后点击我的服务。
2.  从列表中选择 Webhooks，然后点击 Documentation。

应该显示一个屏幕，显示您的 API URL 以及通过 POST 请求传递值的一些字段。

1.  在事件框中键入“notify”(我们事件的名称)。
2.  在值 1 框中键入“test”。

底部产生的命令应该如下所示:

```
curl -X POST -H "Content-Type: application/json" -d '{"value1":"test"}' https://maker.ifttt.com/trigger/notify/with/key/123jhkjh3k4h24j343
```

如果你点击测试它，你应该很快就会在你的手机上收到通知。

如果您在 60 秒内没有收到通知，请尝试重新启动您的设备，以强制应用程序提示通知授权。

如果还是不行，我推荐在 [IFTTT Reddit](https://www.reddit.com/r/ifttt/) 上发帖。

# 从脚本发送通知

好了，我们有了 IFTTT。现在是有趣的部分！我们将编写一个简单的 bash 脚本，让我们从命令行向手机发送通知。这一部分可以在任何类似 Unix 的系统上工作，比如 Windows 上的 Linux、macOS 或 Cygwin。

打开一个终端，然后创建一个名为`notify`的新文本文件。粘贴以下内容，用 Webhook 文档中的密钥替换`<your key>`:

```
#!/bin/basharg=$(echo $1 | sed 's/ /%20/g')
curl -s -o /dev/null "[https://maker.ifttt.com/trigger/notify/with/key/<your key>?value1=$arg](https://maker.ifttt.com/trigger/notification/with/key/cr52Z4jcfAKtL1uKCnbp5SCSXdddCi1yU46ATdasvvf?value1=$arg)"
```

现在保存脚本并使其可执行。这通常可以通过以下方式实现:

```
$ chmod +x notify
```

现在我们可以从命令行测试我们的脚本了:

```
$ ./notify "important stuff"
```

一切正常，你应该很快就会在你的移动设备上收到“通知:重要的东西”的短信。

# 付诸实践

现在我们有了一个可以轻松发布通知的脚本，我们首先需要把它放在系统可以轻松运行它的地方。这种事情我一般用`/usr/local/bin`，所以:

```
$ sudo mv notify /usr/local/bin
```

假设`/usr/local/bin`在您的`PATH`中，那么您现在应该能够简单地从任何目录键入`notify "blah"`并且它将工作。

我们能用这个做什么？当使用`&&` shell 操作符完成另一个命令时，可以执行任何 Unix 命令。这在“[6 Linux 中的 Bash Shell 命令行链接操作符](https://www.thegeekdiary.com/6-bash-shell-command-line-chaining-operators-in-linux/)中有所解释

例如，我们可以这样写:

```
cp my_big_file /some/folder && notify "File copied successfully"
```

或者:

```
make && notify "Code compiled"
```

然而，我们常常不仅想知道某件事是否成功，还想知道它是否失败。这里我们可以使用`||`运算符，例如:

```
make && notify "Compile successful" || notify "Compile failed"
```

# 使用 From 代码

除了来自 shell 的命令行通知之外，我们可能还想从代码中发送通知。大多数语言都支持向 web 服务发送请求，无论是在语言标准库内部还是通过第三方框架。

例如，使用 Python，我们可以如下使用`[urllib.request](https://docs.python.org/3/library/urllib.request.html)`模块:

```
from urllib import requestkey = "<your key>"
message = "something"request.urlopen("[https://maker.ifttt.com/trigger/notify/with/key/%s?value1=](https://maker.ifttt.com/trigger/notification/with/key/cr52Z4jcfAKtL1uKCnbp5SCSXdddCi1yU46ATdasvvf?value1=$arg)%s" % (key,message))
```

最后，我们只是向一个 URL 发送一个 GET 请求。

如果你对脚本通知的有用应用有任何想法，我很乐意在评论中听到。