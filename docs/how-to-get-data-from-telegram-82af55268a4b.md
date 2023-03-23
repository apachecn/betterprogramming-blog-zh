# 如何使用 Python 从电报中获取数据

> 原文：<https://betterprogramming.pub/how-to-get-data-from-telegram-82af55268a4b>

## 关于获取电报通道消息和成员列表的 Python 教程

![](img/493711b800ef91cb585a5eb8ad8acdab.png)

> 2021 年 10 月更新:
> 确保从 GitHub repositry 获得最新的源代码。
> 你也可以观看如何使用这个脚本的视频教程。

出于研究目的，以及为了分析电报通道的内容，您可能需要一个干净的 JSON 格式的通道数据。

我创建了一个 Python 脚本来从电报通道获取数据。它有两个主要文件:一个用于从通道获取成员数据，另一个用于获取通道的消息。

这个脚本将这些数据保存到 JSON 文件中；您可以使用它们进行分析或导入到您的数据库中。

# 要求

您需要安装 Python 3。还有，我用了`telethon`，一个 Python 包来处理 Telegram。

要安装`telethon`，你需要使用一个`pip`命令:

```
pip3 install telethon
```

你可以阅读 [Telethon 的文档](https://docs.telethon.dev/en/latest/)来了解这个包的全部功能。

# 获取您的 Telegram API 证书

为了连接电报，我们需要一个`api_id`和一个`api_hash`。要获得这些参数，您需要登录到您的[电报核心](https://my.telegram.org)并进入 [API 开发工具](https://my.telegram.org/apps)区域。有一个表格你需要填，填完之后你就可以领取你的`api_id`和`api_hash`。

这是 Telegram 的帮助文档，关于如何获得你的 API 证书。

# 在 Python 脚本中创建一个电报客户端

这一部分对于获取渠道成员和渠道消息来说是非常相似的。首先，我们需要基本的进口商品:

我使用`configparser`从配置文件中读取 API 凭证，使用`package.json`将数据转储到 JSON 格式的文件中。

我们从 Telethon 导入我们需要的东西，在我们的脚本中创建一个电报客户端。

如您所知，将您的 Telegram API 凭证存储在您的源代码中是不安全的。如果您将任何类型的凭证直接放入您的源代码，您就在拿您自己的安全和使用该代码的任何人的安全冒险，因为您误导了任何想要使用您的代码的人。

因此，为了避免安全问题，我们将 API 凭证放在另一个名为`config.ini`的文件中。它有这样一个简单的结构:

现在，为了在我们的 Python 脚本中创建一个电报客户端，首先，我们在代码中读取这些凭证:

现在我们已经拥有了所需的一切，我们尝试登录 Telegram 并创建一个客户端对象来获取数据:

Telegram 会授权您的凭证，然后要求您提供验证码和密码(如果您为电报设置了任何验证码和密码的话)。这就像你在应用程序或在线登录你的 Telegram 帐户一样。

请注意，当这个脚本运行时，它可以访问您的电报帐户。确保在安全的环境中运行脚本。

我们现在已经准备好了`client`对象，我们可以使用这个对象来连接和与 Telegram 对话。

# 获取渠道成员

我们将分两步进行。首先，我们从 Telegram 获取所有渠道成员数据，然后将这些数据保存到一个 JSON 文件中。

在这些步骤之前，记得在脚本头中再添加三个导入:

```
from telethon.tl.functions.channels import GetParticipantsRequest
from telethon.tl.types import ChannelParticipantsSearch
from telethon.tl.types import (
PeerChannel
)
```

## 从电报请求频道成员

首先，我们向用户请求一个电报通道。您可以给脚本一个频道的 URL，或者频道的唯一 ID。

因此，[我们获取用户输入](https://github.com/amiryousefi/telegram-analysis/blob/d682a38d3a264cfd11dfaeccebef3f58c1450401/ChannelUsers.py#L39)并将其转换为电报通道:

如果用户给我们一个通道 ID，我们可以将其转换为 PeerChannel 对象。如果用户给我们一个电报频道网址(如 https://t.me/channel)，我们可以直接使用。

下一步是[获取渠道成员](https://github.com/amiryousefi/telegram-analysis/blob/d682a38d3a264cfd11dfaeccebef3f58c1450401/ChannelUsers.py#L52)。首先，你需要知道 Telegram 并不回应你所请求的全部数据，而是分批给出数据。我们可以在每个请求中获得 100 个成员。

我们从偏移量 0 开始设置一个 100 的限制，并创建一个包含通道成员的列表。在一个无限循环中，我们创建了一个对象`GetParticipantsRequest`，它在通道的成员列表中搜索空字符串，并为我们带来所有用户。正如我提到的，我们只能在每个请求中获得 100 个成员。获得成员后，我们检查`participants`对象是否有`users`属性。如果它没有`users`，那就意味着我们得到了所有用户，所以我们打破了无限循环。如果它有`users`，我们向所有成员列表添加新成员，并向偏移量添加收到的成员列表长度，因此下一个请求要求用户从这个偏移量开始。

这个循环继续下去，直到它获得通道的所有成员。

## 将数据存储在 JSON 文件中

这是容易的部分。虽然可以把数据保存到任何数据库，比如 MySQL，MongoDB 等。，最简单的方法是将数据存储在一个 JSON 文件中。但是，如果您有大量数据，最好考虑将其存储在数据库中。

您可以将成员的整个对象存储在 JSON 文件中，但是我更喜欢只存储我需要的内容。因此，我创建了一个列表来添加成员数据，然后将这个列表的 JSON 转储写到一个文件中

简单易行:我创建了一个成员数据的字典，并将它添加到列表中。之后，我将 JSON 转储写到文件中。

下面是获取电报通道成员的完整代码:

 [## amiryusefi/电报分析

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/amiryousefi/telegram-analysis/blob/master/ChannelUsers.py) 

# 获取频道消息

在开始这一步之前，您需要将这些导入添加到脚本的头部:

```
from telethon.tl.functions.messages import (GetHistoryRequest)
from telethon.tl.types import (
PeerChannel
)
```

编辑完导入后，在 Python 代码中创建一个电报客户端与上一节完全相同。此外，从用户处获取频道 ID 或 URL 与上一节中解释的相同。所以，我假设您已经准备好了一份电报`client`，并且已经创建了一个通道对象，我称之为`my_channel`:

向电报客户端发送一个`GetHistoryRequest`对象将返回一个包含消息列表的历史对象。同样，我们对每个请求有 100 条消息的限制。因此，我们在一个无限循环中循环这个请求。在每个请求之后，我们检查历史对象是否有 messages 属性。如果没有，那么我们已经到达了通道中消息的末尾，因此我们可以跳出循环。

我还加了一个`total_count_limit`变量。您可能不想要所有消息，或者获取所有消息可能需要太多时间，因此您可以设置想要从频道获取多少消息。如果将此项设置为 0，脚本将从通道中获取所有消息。

这次设置偏移有点棘手。`GetHistoryRequest`接收到一个`offset_id`，这意味着它应该从什么消息开始获取历史。您需要在每次收到邮件列表时将偏移量设置为最后一个邮件 ID:

```
offset_id = messages[len(messages) - 1].id
```

要将消息保存为 JSON 数据，您需要将消息对象转换为字典。您可以使用`to_dict`函数获取字典格式的消息对象:

```
for message in messages:
    all_messages.append(message.to_dict())
```

代码的最后两行，检查`total_count_limit`是否设置为大于 0。如果 total messages received 是我们需要的邮件总数，并且如果这两个条件都为真，则从循环中断开。

现在您已经有了所有的消息数据，您可以将这个列表存储到一个 JSON 文件中。正如我在上一节中解释的那样，这很简单。

在这里你可以看到完整的代码:

 [## amiryusefi/电报分析

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/amiryousefi/telegram-analysis/blob/master/ChannelMessages.py) 

我分享了这个脚本的完整存储库。你可以看到完整的代码，并且可以自己修改这个库。此外，如果您发现我的源代码有任何改进，我将很乐意接受拉请求。

[](https://github.com/amiryousefi/telegram-analysis) [## amiryusefi/电报分析

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/amiryousefi/telegram-analysis)