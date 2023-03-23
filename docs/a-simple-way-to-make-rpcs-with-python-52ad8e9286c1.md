# 用 Python 制作 RPC 的简单方法

> 原文：<https://betterprogramming.pub/a-simple-way-to-make-rpcs-with-python-52ad8e9286c1>

## 告诉其他系统做什么——简单的方法

![](img/1e1a0ed3fdfc0d1de0e46b623195bf58.png)

照片由 [GuerrillaBuzz Crypto PR](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/network?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

远程过程调用( [RPC](https://en.wikipedia.org/wiki/Remote_procedure_call) s)一直存在，并且是跨分布式系统执行功能的最佳方式之一。有很多方法可以让另一个系统运行一些代码，比如使用 web API 或一些基于套接字的协议，但大多数方法都需要较高的前期开发成本。

使用 web API，您必须提供 HTTP 接口，而其他基于套接字的解决方案可能需要更多样板代码才能起步。当您尝试从头开始构建自己的界面时，尤其如此。当你可以使用经过战斗考验的远程控制时，为什么要浪费时间呢？

在本文中，我们将研究用 Python 实现 RPC 的一种最简单的方法。我们将使用 [RPyC](https://rpyc.readthedocs.io/en/latest/) 库，构建一个简单的分布式客户机/服务器系统，看看实现起来有多简单。

## 构建服务

为了让我们的客户端 RPC 能够工作，我们需要一个基本的服务器设置来监听传入的连接。现在让我们引入 RPyC 库并创建一个新的服务来处理这个问题:

```
# server.pyimport rpyc[@rpyc](http://twitter.com/rpyc).service
class TestService(rpyc.Service):
    [@rpyc](http://twitter.com/rpyc).exposed
    def foo(self):
        return 'foo'

    [@rpyc](http://twitter.com/rpyc).exposed
    def bar(self):
        return 'bar'
```

这是一个简单的 RPyC 服务，它公开了两个方法`foo`和`bar`,这两个方法在被调用时都会返回一个简单的字符串消息。有多种方法来定义暴露的方法，我个人喜欢装饰方法，所以这就是我在这里使用的方法。

每个公开的方法都将出现在 RPC 连接对象的`root`级别，并且可以被任何连接的客户端调用。如果您不希望向客户公开一个方法，只需在您的服务中去掉 decorator 即可。

```
@rpyc.exposed
def my_public_method():
    passdef my_private_method():
    pass
```

还有其他一些特殊的方法，比如`on_connect`和`on_disconnect`，允许你在客户端连接到服务器或者从服务器断开时运行代码。查看文档了解更多实施细节。

既然我们已经定义了服务，我们就可以编写运行服务的实际 RPC 服务器代码了。

## 构建服务器

为了实际使用我们创建的服务，我们需要运行它，然后监听来电。为此，我们将使用 RPyC 中的一个简单的`ThreadedServer` [。这将为每个来电派生出一个新的线程，使它比普通服务器更有效率。它将能够处理稍高的负载，并且实现起来非常简单。](https://rpyc.readthedocs.io/en/latest/api/utils_server.html#rpyc.utils.server.ThreadedServer)

但是请记住，这些线程仅用于传入的调用，如果您想要线程化服务器实例，您需要[自己处理](https://docs.python.org/3/library/threading.html)。为了这个例子，我们将保持它的简单。

现在，让我们对之前的服务代码做一些补充:

```
# server.pyimport rpyc
from rpyc.utils.server import ThreadedServer[@rpyc](http://twitter.com/rpyc).service
class TestService(rpyc.Service):
    [@rpyc](http://twitter.com/rpyc).exposed
    def foo(self):
        return 'foo'

    [@rpyc](http://twitter.com/rpyc).exposed
    def bar(self):
        return 'bar'print('starting server')
server = ThreadedServer(TestService, port=18811)
server.start()
```

这里我们仍然使用完全相同的服务，但是现在我们将它传递给我们的`ThreadedServer`并启动它。它将在端口`18811`上监听传入的 RPC 请求。

您可以使用以下语法执行服务器:

```
python3 server.py
```

接下来，让我们让这个服务器保持运行，并测试我们的客户机，让它开始尝试一些调用。

## 构建客户端

为了让我们制作 RPC，我们需要一个主机和端口来连接。因为我们只是在测试我们的服务，所以我们可以通过`localhost`完成所有这些工作。在生产中，您可能会连接到远程服务器的 IP 地址或域名。

让我们看看简单的环回客户端是什么样子的:

```
import rpycconnection = rpyc.connect('localhost', 18811)print(connection.root.foo())
print(connection.root.bar())
```

在这个例子中，我们在端口`18811`上连接到`localhost`，这是我们的服务器(*应该还在运行*)监听传入连接的地方。一旦我们连接上，我们就可以开始调用服务中定义的方法。

我们调用返回预期字符串的`foo`和`bar`方法。这些方法位于`root`层(或 [NetRef](https://rpyc.readthedocs.io/en/latest/api/core_netref.html) 层)之下，所以我们需要做的就是在那里寻找它们。您还可以创建自定义的 NetRefs，但是对于本文来说，这有点复杂。

祝贺你，你现在已经用 Python 制作了一些 RPC！

感谢您的阅读！看看我下面的几个帖子:

*   [*Python 模块使得处理 JSON 更快*](/python-modules-that-make-handling-json-even-faster-f577d8948a5)
*   [*加速开发的 7 个外壳快捷键*](/7-shell-shortcuts-to-speed-up-development-439943247eea)