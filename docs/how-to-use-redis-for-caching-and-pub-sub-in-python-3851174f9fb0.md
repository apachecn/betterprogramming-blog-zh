# 如何在 Python 中使用 Redis 进行缓存和发布/订阅

> 原文：<https://betterprogramming.pub/how-to-use-redis-for-caching-and-pub-sub-in-python-3851174f9fb0>

## 了解 Python 中 Redis 的常见用例

![](img/21010e0711b9e1ab603b1fca66757ea5.png)

图片来自 [Unsplash](https://unsplash.com/photos/w1K9Ug_pjXw) 。

[Redis](https://medium.com/codex/essentials-of-redis-all-you-need-to-know-as-a-developer-73da5d2fdc0b) 是一个开源的内存数据结构存储，可以用作内存键值数据库、缓存系统和发布/订阅消息代理。Redis 的特殊之处在于，它的所有数据都存储在内存中，而不是存储在磁盘上，这使得它非常快，是一种流行的缓存选项。

在[之前的文章](https://medium.com/codex/essentials-of-redis-all-you-need-to-know-as-a-developer-73da5d2fdc0b)中，介绍了 Redis 数据类型和常用命令的要点。在本文中，我们将关注如何在 Python 中使用 Redis 进行缓存和发布者/订阅者(pub/sub)。

您可以直接在计算机上安装 Redis 服务器。然而，出于学习的目的，推荐使用 Docker 容器来测试 Redis，因为您可以随时使用最新版本的 Redis 进行测试。要使用 Docker 容器启动 Redis 服务器，请在 shell 中运行以下命令。

高端口 16379 用于避免机器上潜在的端口冲突。此外，还创建了一个 Docker 网络，使 Redis 服务器与`redis-cli`的连接更加容易:

您现在可以使用`redis-cli`运行常见命令，如[上一篇文章](https://medium.com/codex/essentials-of-redis-all-you-need-to-know-as-a-developer-73da5d2fdc0b)中所示。但是，本文的重点是如何在 Python 中使用 Redis，所以我们继续。

# **在 Python 中使用 Redis 的基础知识**

要用 Python 访问 Redis 服务器，您需要一个 Python Redis 客户机。推荐的是`[redis-py](https://redis.io/clients#python)`，比较成熟，支持也不错。它目前被视为 Python 的“必由之路”。

要使用`redis-py`，我们应该把它安装在我们的电脑上。建议将它安装在一个[虚拟环境](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)中，这样它就不会与现有的库混淆。为简单起见，我们将使用 [*conda*](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2) 来创建一个虚拟环境:

命令行上的`(redis)`表示虚拟环境已成功创建并激活。如果你想了解更多关于*康达*，[这篇文章](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)可以作为很好的参考。

然后要安装`redis-py`，运行:

```
$ **pip install redis**
```

注意要安装的库是`redis`，不是`redis-py`。为了更容易地以交互方式运行 Python 命令，我们还将在虚拟环境中安装 iPython:

```
$ **pip install ipython**
```

然后我们可以启动 iPython 并与 Redis 交互:

如果您的 Redis 服务器启用了身份验证，您可以用`password`参数指定密码。如果您的 Redis 服务器有多个数据库，您还可以指定`db`参数。默认的数据库号是 0，这是在您没有指定数据库号时使用的默认数据库号。

请注意，您应该正确指定端口，这里是自定义的高端口 16379，而不是经典的 6379。还要注意 Redis 的键和值只能是字节、字符串、整数和浮点数。为了最佳实践，我们通常应该只使用有意义的字符串作为键。但是，对于具有非基本类型(如对象或数组)的值，必须先将其转换为字符串或字节，然后才能在 Redis 中设置为值。`json.dumps`函数通常用于将 Python 中的字典或列表转换成 JSON 字符串。

另一方面，正如我们所见，在 Python 中，默认情况下，所有响应都以字节形式返回。如果我们想要解码来自 Redis 客户端的所有字符串响应，我们可以在创建客户端时指定`decode_responses` 和`encoding`参数:

如果你不熟悉字符串编码/解码和 Unicode/UTF-8 的概念，这篇文章可能会有所帮助。

要删除 Redis 密钥，只需使用客户端的`delete`方法:

结果中， *1* 表示密钥删除成功， *0* 表示密钥不存在。

当我们有许多 Redis 键需要检查时，有一个方便的方法叫做`scan_iter`，它类似于 [SCAN](https://medium.com/codex/essentials-of-redis-all-you-need-to-know-as-a-developer-73da5d2fdc0b) 命令，但使用起来要方便得多，因为我们不需要记住和指定光标:

这里的[冒号](https://medium.com/codex/essentials-of-redis-all-you-need-to-know-as-a-developer-73da5d2fdc0b) ( `:`)只是用来分隔对象类型和 id，实际上没有任何特殊含义。`scan_iter`方法期望一个 [Redis 模式](https://redis.io/commands/keys)作为输入。如果没有指定模式，将返回所有键。特别是，星号“`*`”匹配任意数量的任意字符。它相当于正则表达式中的“`.*`”模式。

上面我们已经介绍了 Redis 在 Python 中非常基本的用法。Python 中的大多数 Redis 方法都有其本地的`redis-cli`命令对应物。如果你有兴趣或者需要使用其他方法，可以参考[上一篇文章](https://medium.com/codex/essentials-of-redis-all-you-need-to-know-as-a-developer-73da5d2fdc0b)重点介绍的原生`redis-cli`命令。

现在让我们把重点放在 Redis 管道和 pub/sub 上，它们在`redis-cli`中不常用，但在驱动程序中更常用(在本文中是 Python)。

# **Redis 管道**

对于 Redis，管道是同时执行多个命令的一种方式。命令将被缓冲，只有一个请求将被发送到 Reids 服务器。通过这种方式，Redis 服务器和客户端之间的通信开销显著降低，速度/效率得以提高。当您需要顺序运行许多 Redis 命令时，管道非常有用。

管道类似于 SQL 数据库中的[事务](https://medium.com/codex/understand-the-basics-of-locks-and-deadlocks-in-mysql-part-i-92f229db0a)。但是，这里不是先启动一个事务，然后提交，而是先创建一个管道对象，然后执行:

正如我们所看到的，管道使用起来非常简单。它有更多的高级设置，这使得它可以在更复杂的情况下工作。

# **Redis 发布/订阅**

Redis 是一个常用的轻量级消息/队列或发布/订阅(pub/sub)系统。例如， [Airflow](https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html) 使用它作为代理将消息从调度器转发到工作器。

与其他消息/队列系统一样，在发布和接收消息之前，我们需要创建一个通道和订阅。在一些消息/队列系统中，如 Google 云平台的 [Pub/Sub](https://lynn-kwong.medium.com/how-to-use-google-pub-sub-to-build-an-asynchronous-messaging-system-in-python-3b43094627dc) 服务中，通道有时被称为 [topic](https://cloud.google.com/pubsub/docs/overview#core_concepts) 。

实际上，我们不需要显式地创建通道。频道是在第一次订阅时自动创建的，如上所示。而且，我们可以同时订阅多个频道。我们甚至可以通过模式订阅多个频道，我们很快就会看到下面的`psubscribe`方法。

现在让我们向渠道发布一些消息:

应该注意，Redis 客户机(`redis_cli`)用于将消息发布到通道，而不是上面创建的 PubSub 对象。返回的数字是消息传递到的订户的数量。让我们创建一个新的订阅，看看数字是否会相应地改变。

在这个例子中,`psubscribe`意味着订阅将由模式创建。创建的订阅将侦听与指定模式匹配的所有频道。

正如我们所看到的，当我们发布到`channel-*`时，返回是 1，这意味着消息只发布到一个订阅者，即使用模式`channel-*`创建的订阅者。但是，当消息再次发布到`channel-1`时，返回值变为 2，这意味着消息发布到了两个订阅者。实际上，只创建和使用了一个 PubSub 对象。然而，这个 PubSub 对象有两个订阅/订户，一个订阅到频道`channel-1`，另一个订阅到所有匹配`channel-*`的频道。

现在让我们从系统中获取发布的消息。为此，我们需要调用 PubSub 对象的`get_message`方法。

`get_message`方法获取下一条消息(如果有)，否则不获取。它返回一个包含四个键的字典:

*   `type` —数据的类型。值可以是`subscribe`、`psubscribe`、`message`等。`message`表示实际发布的数据，其他表示订阅类型，可以看作元数据。我们通常只对`message`类型的数据感兴趣。
*   `pattern` —通道的模式。对于除了`pmessage`类型以外的所有消息，它都是 None，如上所示。在本例中，这意味着消息被发布到与模式`channel-*`匹配的通道。
*   `channel` —消息发布到的渠道。
*   `data` —实际发布的消息数据。对于“元数据”订阅，它是调用`get_message`方法时 PubSub 对象的订阅数。对于消息类型，它是实际发送的消息。

如果你和我一样好奇，那么当创建一个新的订阅和取消订阅时，会发布一个“元数据”消息。

通常情况下，您不会太在意元数据，只需要处理发布的真实消息。

从上面的简单例子中我们可以看出，创建一个通道(主题)和订阅，然后用 Redis 发布/接收消息是非常简单的。然后，您可以创建一些逻辑来解析接收到的数据，并在您的应用程序中使用它。

本文介绍了 Python 中 Redis 最常见的用例。结合[上一篇关于使用原生 Redis 命令的文章](https://medium.com/codex/essentials-of-redis-all-you-need-to-know-as-a-developer-73da5d2fdc0b)，您现在应该对 Redis 有了相当好的理解，并可以开始在工作中使用它。

相关文章:

*   [如何使用 Google Pub/Sub 在 Python 中构建异步消息传递系统](https://lynn-kwong.medium.com/how-to-use-google-pub-sub-to-build-an-asynchronous-messaging-system-in-python-3b43094627dc?source=your_stories_page----------------------------------------)