# 如何使用 Python 轮询套接字

> 原文：<https://betterprogramming.pub/how-to-poll-sockets-using-python-3e1af3b047>

## 一种更快、更有效的与套接字交互的方式

![](img/89fd494987d9c67f028f48fcf37d319f.png)

照片由[马腾·范·登·霍维尔](https://unsplash.com/@mvdheuvel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/files?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果您正在编写从网络套接字读取或写入的代码，您会希望这些操作尽可能地快速高效。也许你想在不影响速度和阻塞每个呼叫的情况下关注多个插座。在大多数情况下，从底层操作系统获取关于套接字上是否存在数据的信号实际上是一个非常简单的过程。

如果您正在使用 Python，您可以访问一个强大的库来跟踪套接字状态。图书馆。

使用`select`你可以`poll`一个特定的套接字或一组套接字用于任何输入或输出数据。这允许您观察多个套接字，而无需不断尝试与它们直接交互。与其试图获取不存在的数据，为什么不让操作系统告诉你它何时存在呢？

`poll`的一个主要优势是它允许你同时处理多个插座，而不会因为等待其中一个而卡住。

让我们来看看。

# 向套接字发送数据

为了了解`poll`是如何工作的，让一些测试数据流经几个套接字是有帮助的。这将有助于我们直观地看到数据何时出现在缓冲区中，以及当`poll`告诉我们从套接字读取数据然后继续循环时，它会如何表现。

下面是一个简单的`sender`服务器的代码片段:

```
# sender.pyimport socket
from time import sleepsocket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)while 1:
    socket.sendto(b'hello', ('127.0.0.1', 9998))
    print('sent data to socket_1')
    sleep(1)
    socket.sendto(b'hello', ('127.0.0.1', 9999))
    print('sent data to socket_2')
    sleep(1)
```

让我们来分析一下这是怎么回事:

*   首先，我们初始化一个新的 UDP 套接字。
*   接下来，我们开始一个无限循环。
*   在循环内部，我们使用回送地址将`hello`发送到端口`9998`和`9999`的两个套接字。这是我们将在`receiver`端监听的地方。
*   在每次发送操作之间，我们打印一条消息，说明数据已经发送，然后休眠一秒钟。

这是保持发送到两个套接字的数据流一致的简单方法，我们将等待使用`poll`。现在，让我们看看如何构建这个场景的另一面。`receiver`服务器。

# 设置接收器

为了`poll`我们的套接字，我们需要创建一些。在本例中，我们将设置两个 UDP 套接字，并让它们使用主机上的环回地址监听两个不同的端口。这与我们刚刚在`sender`代码中使用的地址和端口集相同:

```
# receiver.pyimport socket
import selectsocket_1 = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
socket_2 = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)socket_1.bind(('127.0.0.1', 9998))
socket_2.bind(('127.0.0.1', 9999))poller = select.poll()
poller.register(socket_1, select.POLLIN)
poller.register(socket_2, select.POLLIN)while 1:
    evts = poller.poll(5000)
    for sock, evt in evts:
        if evt and select.POLLIN:
            if sock == socket_1.fileno():
                socket_1.recvfrom(4096)
                print('received poll event from socket_1')
            if sock == socket_2.fileno():
                socket_2.recvfrom(4096)
                print('received poll event from socket_2')
```

同样，让我们分解这段代码，看看它到底在做什么:

*   我们设置两个 UDP 套接字，然后将它们绑定到我们从`sender`使用的环回地址和端口。
*   接下来，我们创建一个`poll`实例，并为其创建`register`两个套接字。该方法接受套接字作为第一个参数，接受“[事件](https://docs.python.org/3/library/select.html#select.poll.register)”作为下一个参数。每个事件对应于套接字上的一个动作。这可能意味着数据正在输入或准备输出。在我们的例子中，我们将使用`POLLIN`来表示套接字上的数据已准备好接收。
*   开始一个无限循环，我们开始轮询。`poll`的参数是以毫秒为单位的超时。如果在超时时间内没有收到任何事件，此操作将会失败。因为我们是在一个无限循环中，这没什么大不了的，我们只是重新开始循环，等待事件再次发生。我们还将超时设置为高于我们发送`sender`代码的速率，因此理想情况下永远不会超时。
*   收集事件后，我们通过套接字和事件类型遍历它们。
*   对于每一对，我们将带有数据的轮询套接字文件描述符与我们的原始套接字对象进行比较，如果它们匹配，我们就从套接字中读取。`recvfrom`的参数是以字节为单位的缓冲区大小。
*   最后，我们打印一条消息，说明我们收到了一些数据，循环重新开始寻找新的事件。

# 把所有的放在一起

现在我们已经有了我们的`sender`和`receiver`让我们试一试。首先启动`receiver`。这应该什么也不输出，只是坐在那里等待流量:

```
python3 receiver.py
```

接下来，启动`sender`:

```
python3 sender.py
```

现在，您应该看到消息在两端输出。`sender`应该告知何时向每个套接字发送消息。`receiver`应说明何时接收到消息以及在哪个套接字上:

```
>>> sender
sent data to socket_1
sent data to socket_2
sent data to socket_1
sent data to socket_2>>> receiver
received poll event from socket_1
received poll event from socket_2
received poll event from socket_1
received poll event from socket_2
```

成功运行这个示例代码后，您可以开始将`poll`方法应用到您自己的代码中。您可以添加不同类型的轮询事件，以创建更高效的写入或错误处理操作。

查看官方的`select`库[文档](https://docs.python.org/3/library/select.html#select.poll.register)了解关于实现`poll`的细节。

# 为什么要投票？

如果不小心的话，读写多个套接字可能会成为一项非常昂贵的操作。根据数据量和套接字数量，正常的阻塞操作最终会导致性能下降。使用`poll`,你可以通过让操作系统告诉你何时执行读写操作来避免大量的开销。这比一次尝试一个插座更有效。

例如，如果一个套接字使用`poll`在另一个没有数据的套接字之前接收到大量的数据，您会马上得到通知。你不必等待没有数据的套接字。随着套接字和操作数量的增加，您应该会看到使用`poll`后效率有了相当大的提高。