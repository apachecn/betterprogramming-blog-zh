# Nameko 简介:构建微服务的 Python 框架

> 原文：<https://betterprogramming.pub/building-microservices-with-nameko-c2ddd203130b>

## 利用 Nameko 的能力构建可扩展的健壮微服务系统

![](img/222d57908a66bc624a47ba27c06dfe76.png)

图片来源:[克日什托夫·尼沃尔尼](https://unsplash.com/@epan5)

在本文中，我们将了解 Nameko 及其作为微服务框架的功能。

# Nameko 是什么？

Nameko 是一个遵循微服务架构设计的框架，用于在 Python 中构建轻量级、高度可伸缩和容错的服务。

它内置了对以下方面的支持:

*   AMQP 上空的 RPC
*   AMQP 上的异步事件(发布-订阅)

# 为什么是奈美子？

[Nameko](https://nameko.readthedocs.io/en/stable/) 使您能够构建一个服务，该服务可以响应 RPC 消息，调度特定动作的事件，并侦听来自其他服务的事件。它还可以为不会说 AMQP 语的客户提供 HTTP 接口。

让我们创建一个基本的 Nameko 服务并试验它的功能。

# 设置基本环境

首先，你需要安装的 [Docker。我们将使用 Python 3，所以请确保您也安装了它。为了运行 Nameko，我们需要 RabbitMQ。它将负责我们 Nameko 服务之间的通信。](https://docs.docker.com/get-docker/)

## 安装

```
$ pip install nameko 
```

## 启动 RabbitMQ 容器

```
$ docker run -p 5672:5672 — hostname nameko-rabbitmq rabbitmq:3
```

# Nameko 的 Hello World

Nameko 服务只是一个 Python 类。该类将逻辑封装在其方法中，并将任何依赖项声明为属性。

继续创建一个名为`Service.py`的文件，其内容如下:

```
from nameko.rpc import rpc

class Service:
    name = "service"

    @rpc
    def receive_event(self, event):
        return f"Event Received: {event}"
```

让我们运行我们的例子。如果 RabbitMQ 正在运行，只需运行:

```
$ nameko run Service
```

Nameko 实现了自动服务发现，这意味着当通过 AMQP 调用 RPC 方法时，Nameko 将尝试自己找到相应的 RabbitMQ 服务。

要测试我们的服务，请在另一个终端中运行以下命令:

```
>>> n.rpc.service.receive_event(event={‘message’: ‘Hello World!!’})
```

当调用 RPC 入口点时，会创建一个 Nameko worker。worker 只是服务类的一个无状态实例，这使得它本质上是线程安全的。默认情况下，服务的最大工作线程数设置为 10。

[点击此处阅读更多关于 Nameko 工人的信息](https://nameko.readthedocs.io/en/stable/key_concepts.html/#workers)。

如果最大工作线程数设置为 1，那么一次只会执行一个 Nameko 工作线程，也就是说，它将表现为一个常规队列。

# 如何在两个 Nameko 服务之间进行通信

为了从一个 Nameko 服务到另一个服务进行通信，反之亦然，Nameko 提供了一个`RpcProxy`构造。你可以这样使用它:

```
from nameko.rpc import rpc, RpcProxy

class SenderService:
    name = "sender_service"
    receiver_service_proxy = RpcProxy("receiver_service")

    @rpc
    def send_event(self, event):
        return self.receiver_service_proxy.receive_event({'message': 'Hello World!!'})

class ReceiverService:
    name = "receiver_service"

    @rpc
    def receive_event(self, event):
        return f"Event Received: {event}"
```

# 名片和非名片服务之间的通信

有些情况下，我们需要从不是 Nameko 服务的地方调用 Nameko 服务，比如 API 服务或 cron 作业。你可以这样做:

```
from nameko.standalone.rpc import ClusterRpcProxy

AMQP_URI = "pyamqp://user:paswword@hostname"

config = {
    'AMQP_URI': AMQP_URI
}

with ClusterRpcProxy(config) as cluster_rpc:
    cluster_rpc.service.receive_event({'message': 'Hello World!!'})
```

# 并发

Nameko 构建在`[eventlet](http://eventlet.net/)`库之上，通过`greenthreads`提供并发性。

绿色线程与操作系统线程不同，它们相互协作让步，而不是由操作系统抢先调度。当服务的 I/O 负载较重时，这种行为被证明是有利的。

一个绿色线程只有在忙于执行 I/O 时才会让出控制权，这给了另一个绿色线程执行的机会，从而允许服务使用共享数据结构，而无需使用锁和其他同步机制。

让我们通过修改上面的代码在实践中试验 Nameko 并发性:

```
from time import sleep
from nameko.rpc import rpc

class Service:
    name = "service"

    @rpc
    def receive_event(self, event):
        sleep(5)
        return f"Event Received: {event}"
```

我们正在使用时间模块中的`sleep`，这是一个阻塞调用。然而，当使用`nameko run`运行我们的服务时，nameko 会自动为非阻塞调用(如`sleep(5)`)打上阻塞补丁，即使其异步。

对我们服务的单个 RPC 调用的响应时间将是 5 秒。现在，如果我们一次对同一个 RPC 发出 10 个调用，那么得到所有 10 个调用的响应需要多长时间？

让我们在 nameko shell 中运行以下代码:

```
def time_concurrent_invocations():
    start_time = time.perf_counter()
    responses = []
    num_concurrent_calls = 10
    for i in range(num_concurrent_calls):
        response = n.rpc.service.receive_event({'message': f'Worker {i+1}'})
        responses.append(response)

    for response in responses:
        print(response.result)

    end_time = time.perf_counter()

    print(f'Total Time: {round(end_time-start_time, 3)}')

time_concurrent_invocations()
```

这个例子只运行了大约五秒钟。每个工人将被阻塞等待睡眠调用结束，但这并不阻止另一个工人开始，在行动中隐含让步。

如果您更改上面代码片段中的`num_concurrent_calls = 20`，执行将在 10 秒内完成。

# 异步发布-订阅

让我们假设，我们现在必须执行一个异步任务，比如在云上发送通知或上传文件:

```
from nameko.events import EventDispatcher, event_handler
from nameko.rpc import rpc

class MessageService:

    name = "message_service"

    dispatch = EventDispatcher()

    def time_consuming_function(self, payload):
        self.dispatch("heavy_payload_event", payload)

    @rpc
    def receive_message(self, event):

        if event['payload']:
            self.time_consuming_function(event['payload'])

        print(f'Message Received: {event['message']}')

class TimeConsumingService:
    name = "time_consuming_service"

    @event_handler("message_service", "heavy_payload_event")
    def time_consuming_event_handler(self, payload):
        pass
```

当`receive_message`处理一个带有有效负载的事件时，它调用`time_consuming_function`，后者通过在一个单独的绿色线程中调用`time_consuming_event_handler`来利用`EventDispatcher`以异步方式处理有效负载。这里的调用方线程不等待事件处理程序返回响应，从而允许调用方线程更快地完成执行并接受进一步的请求。

# 可攀登的

我们一直只使用一台服务器，运行一个 RabbitMQ 实例。在生产环境中，您可能希望任意增加运行调用过多的服务的节点数量。

为了模拟服务伸缩，让我们从并发部分重新审视我们的服务。使用`nameko run Service`打开另一个终端，像以前一样运行服务。这将启动另一个服务实例，有可能运行十个以上的工作线程。现在，尝试使用`num_concurrent_calls = 20`再次运行该代码片段。现在应该再花五秒钟来运行。当有多个服务实例运行时，Nameko 将在可用的实例中循环调用 RPC 请求。

事实上，您可以配置这些服务，使它们可以在完全不同的机器上运行，并独立地扩展它们。您所需要做的就是将这些服务指向同一个 RabbitMQ 代理。

使用代理 URI 创建一个配置文件:

```
# config.yaml
AMQP_URI: amqp://<rabbitmq-ip>:5672/
```

使用以下命令在不同的计算机上运行这些服务:

```
$ nameko run <nameko_service> --config config.yaml
```

# 容错的

Nameko 具有高度的健壮性和容错能力，因此，即使服务群集中的一个或多个节点出现故障，它也能继续正常运行，直到至少有一个健康的节点保持运行。

尝试运行服务的 3 个实例，并使用`num_concurrent_calls = 50`执行测试片段。一旦执行了测试片段，就终止一两个`Service`实例。丢失的消息将被重新路由到健康节点，从而避免消息丢失。

这种行为是由于在 worker 执行成功完成后消息被*确认*的事实，如果在交付后但在确认前连接丢失，RabbitMQ 将回收并重新交付消息。

# 如果 RabbitMQ 服务器死亡，队列中还有消息，会发生什么情况？

Nameko 为 RPC over AMQP 创建的队列默认设置了`delivery_mode=PERSISTENT`。这告诉 RabbitMQ 将消息保存到磁盘。

然而，当 RabbitMQ 已经接受了一个消息，但还没有保存它时，有一个短的时间窗口，这意味着持久性保证不是很强。

为了解决这个问题，Nameko 默认使用 [publisher 确认](https://www.rabbitmq.com/confirms.html)。确认会造成性能损失，但可以保证消息不会丢失。

# 结论

Nameko 旨在帮助您使用微服务构建系统，并从单个服务的单个实例扩展到包含许多不同服务的多个实例的集群。

要了解更多关于 Nameko 的信息，请查看 [Nameko 文档](https://nameko.readthedocs.io/en/stable/)并加入 [Nameko 讨论](https://discourse.nameko.io/)。