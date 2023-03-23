# 用 Node.js 实现 RabbitMQ

> 原文：<https://betterprogramming.pub/implementing-rabbitmq-with-node-js-93e15a44a9cc>

![](img/baf7c05740c09e49f83d05b5c429759e.png)

本文是[系列文章](https://medium.com/@pankaj.panigrahi/list-of-node-js-articles-ededa6dd304b)中的第 8 篇，它将帮助您掌握 Node.js 背后的不同概念，并使您能够创建生产就绪的应用程序。这篇文章期望读者了解巴别塔以及如何设置它。如果你需要知道如何设置，请阅读这篇[文章](https://medium.com/@pankaj.panigrahi/using-babel-and-other-dependencies-in-node-js-18f74973c1f0)。

如果您需要抑制发送到 API 或 workers 的请求，或者您希望事情异步发生，那么使用消息队列是显而易见的。如果你还想要更多的理由，请浏览下面这篇文章。

[](https://stackify.com/message-queues-12-reasons/) [## 消息队列&使用消息队列的 12 个理由

### 在 Stackify，我们从您的应用程序接收大量数据到我们的 Retrace APIs。我们会尽快对所有数据进行排队…

stackify.com](https://stackify.com/message-queues-12-reasons/) 

一些流行的消息队列有:

*   兔子 q
*   卡夫卡
*   IBM MQ
*   ActiveMQ
*   RocketMQ
*   Qpid

RabbitMQ 是最流行的开源消息代理。RabbitMQ 是轻量级的，易于部署。它支持多种消息协议。RabbitMQ 有不错的表现和强大的社区。如果您的需求是每秒处理数千条消息，我建议您使用 RabbitMQ。

我见过人们没有正确地了解他们的需求就跳到卡夫卡那里。毫无疑问，Kafka 提供了比 RabbitMQ 等消息队列更好的性能和特性，但是设置起来相当复杂。Kafka 服务器使用 [Zookeeper](https://zookeeper.apache.org/) 作为集群成员和路由。如果有效负载不是那么高，设置两个服务器将是不必要的。

在本文中，我们将探索 RabbitMQ 以及如何将它与`node.js/express`API 一起使用。

老实说，RabbitMQ 文档写得太好了，你不需要其他的东西。但是我们需要一个 RabbitMQ 教程作为这个系列的一部分。所以我将在一定程度上总结文档，看看我们如何构建一个 REST API 来处理 RabbitMQ 驱动程序。

RabbitMQ 中正常的工作队列设置如下:

![](img/465c8ed0204ac3140a44d5f60f435b3b.png)

RabbitMQ 中的工作队列设置

RabbitMQ 设置中有 3 个重要部分:

1.  制片人。发送消息的程序是*生产者。*(图中用 P 表示)。
2.  排队。消息存储在一个*队列中。*它本质上是一个大的消息缓冲区。一个队列只受主机的内存和磁盘限制。(图中用红色矩形框表示)*。*
3.  消费者。一个*消费者*是一个主要等待接收消息的程序。(图中以 C1 & C2 为代表)*。*

一个队列可以有多个生产者和消费者。但是在正常的用例中，我们不需要多个生产者，因为将消息放入队列非常简单和快速。但是使用队列中的消息并基于该消息执行任务通常需要时间。因此，我们倾向于为一个队列实现多个消费者，以便当一个消费者忙碌时，另一个消费者可以从队列中读取数据并执行任务。

执行一项任务可能需要几秒钟。您可能想知道，如果一个消费者开始了一项长期任务，但只完成了一部分就去世了，会发生什么。在这种情况下，如果 worker 崩溃，我们将丢失它刚刚处理的消息。为了确保消息不会丢失，RabbitMQ 支持消息**确认**。

消费者发回一个`ack` (nowledgement)消息，告诉 RabbitMQ 已经收到并处理了一个特定的消息，RabbitMQ 可以删除它。RabbitMQ 将理解消息没有被完全处理，并将它重新排队。如果同时有其他消费者在线，它会迅速将它重新交付给另一个消费者。

但是如果 RabbitMQ 服务器崩溃了呢？队列中的消息会丢失吗？

默认情况下，是的。但是有一些步骤可以确保您的队列和消息在您的磁盘上是持久的。以便在服务器崩溃的情况下，您可以重新启动服务器并处理消息。

首先，我们必须确保我们的队列被声明为`durable`。我们可以在 RabbitMQ 管理控制台中或者通过代码来实现。然后，我们应该通过将`persistent`字段设置为`true`来确保队列中的消息是持久的。

当我们编码时，我们将检查它们。

我们可以通过[安装 RabbitMQ](https://www.rabbitmq.com/download.html) 在本地机器上安装 RabbitMQ，但是为了增加趣味，我们将像在生产环境中一样使用远程 RabbitMQ 服务器。

有一家公司很慷慨，为开发者提供免费计划。

[](https://www.cloudamqp.com/plans.html) [## 计划和定价- CloudAMQP

### 我们为您的所有需求提供灵活的 RabbitMQ 计划。您在找什么？按秒计费，随时取消…

www.cloudamqp.com](https://www.cloudamqp.com/plans.html) ![](img/296d818a058ec58fe4ee664520bf9b06.png)

他们的免费计划足以达到学习的目的。在他们的网站上创建一个帐户，并创建一个实例。

创建实例后，单击“RabbitMQ 管理器”按钮。

![](img/8404e9b120b408a1bd6d289efe12e17a.png)

这将打开标准的 RabbitMQ 管理控制台。让我们在同一个控制台中创建一个持久队列。

![](img/a30a0b61c41a0fdffa02c4de6e3f1c78.png)

我们也可以从代码中创建一个队列。队列应该在几秒钟内准备好。

![](img/0973cc23a8f34c65c6ba0389c04ae0e5.png)

现在我们需要从代码中连接 RabbitMQ 实例，为此我们需要连接 URL。单击“实例名称/行”以获取连接和其他实例详细信息。

![](img/a6854e2d505b21b5c5e8fef53e8887fd.png)

您可能会看到许多简单的程序只是将一条消息放入队列中。但是在现实生活中，更有可能调用 REST API，然后将消息放入队列中。

让我们从我之前创建的锅炉板[开始。如果你一开始就跟着系列走，锅炉板块应该很清楚了。](https://github.com/pankaj805/medium-00_boiler_with_db)

首先安装下面的库。

```
npm install amqplib --save
```

在第 5 篇文章[中，](https://medium.com/@pankaj.panigrahi/getting-data-from-mongodb-creating-an-api-key-validation-middleware-in-express-944382205d3e)我们看到了重用 MongoDB 连接的一种方法是将它注入请求对象。在这篇文章中，我们将看到另一种方法来实现同样的事情。

创建以下文件`services/MQService.js`

导入库并将连接 url 保存在一个常量变量中。

```
import amqp from 'amqplib/callback_api';const CONN_URL = 'amqp://gsgmnvnl:NITe9ThLkXQvKVLl7L6gEtMllb6obQmw@dinosaur.rmq.cloudamqp.com/gsgmnvnl';
```

我们可以使用由 [AMQP](https://www.amqp.org/) 提供的`connect()`方法来创建一个连接。为了从队列中发送或接收消息，我们必须使用通道对象。做到这一点的方法是:

```
let ch = null;
amqp.connect(CONN_URL, function (err, conn) {
   conn.createChannel(function (err, channel) {
      ch = channel;
   });
});
```

然后，让我们创建一个方法，向实现了`sendToQueue`方法的队列发送消息。

```
export const publishToQueue = async (queueName, data) => {
   ch.sendToQueue(queueName, new Buffer(data));
}
```

MQService 文件看起来像这样:

我们还添加了一个进程监听器，在我们终止进程时关闭 RabbitMQ 连接。

让我们在我们的`routes/User.js`文件中添加另一条路线。首先让我们导入我们在服务中创建的`publishToQueue`方法。

```
import {publishToQueue} from '../services/MQService';
```

然后添加以下路由方法。

我们正在将消息发送到队列，并且我们正在发送一个成功响应。

现在让我们来看看消费者。创建一个新项目，我们将在其中定义我们的员工/消费者。

在项目中创建一个类似`worker-1.js`的文件。

![](img/a840357a2c4e6e19b934eb74cd29b2ed.png)

最佳实践是保持工作器精简，因为您可能需要生成相同的多个实例来处理流量。

为了简单起见，我在这里没有使用巴别塔。所以在 worker 文件中看不到任何 ES6。

在 worker 文件中编写以下代码。

在这里，我们首先导入我们的依赖项，然后进行我们之前进行的连接初始化。一旦我们有了通道连接，我们就使用 consume 方法，它有三个参数:

`consume` ( `queue_name`、`callback`、`ack`)

第一个参数是队列名称，第二个参数是回调方法，一旦我们在队列中获得一条消息，就会调用该方法。第三个参数是确认设置。

```
noAck:true
```

如果我们将`noAck`字段设置为`true`，那么队列将在从队列中读取消息时删除该消息。

在回调函数中，我们在延迟 4 秒后打印消息，只是为了模拟 I/O 操作。

```
function (msg) {
  console.log('.....');
  setTimeout(function(){
    console.log("Message:", msg.content.toString());
  },4000);
}
```

![](img/045968ae1dc043bd07381ad67fa0543d.png)

首先，让我们只运行生产者。

![](img/91ad03c39e7cae011aa2edb2400651db.png)

像我们在之前的文章中所做的那样，使用 REST API。提及如图所示的`queueName`和`payload`。

您应该会立即得到回应，因为我们不会在这里等待消费者执行任务。

由于我们没有启动我们的 worker，我们应该在队列中看到消息。

![](img/5d590a54b3b0cd19c222c9e036a09db4.png)

现在让我们开始工作。

![](img/4fb4dd91b71176d02cb0f76815e5a861.png)

我们应该在 4 秒钟后看到消息。

用不同的有效负载再次点击 API。

![](img/2777d31368231ac50c978345e2460e47.png)

worker 从队列中读取消息，并在 4 秒钟后打印出来。
完美！

然而，在现实生活中，消费者可能会在执行某些操作时崩溃。在这种情况下，我们希望消息回到队列中，这样消息就可以被另一个工作线程使用，或者在我们再次生成它时被同一个工作线程使用。

为了实现这一点，我们必须做几件事。

首先要做的，是改变`consume`方法中的第三个参数值。`noAck : false`

由于我们已经将`noAck`设置为`false`，我们必须显式调用`channel.ack()`方法。现在我们的`consume`方法看起来像:

我们还增加了睡眠时间，这样我们就很容易重现这个场景。

让我们将另一条消息推送到队列中，然后在八秒钟的等待结束之前杀死这个工人。

![](img/12d3364d2184a37798e64d05e98b7668.png)

在上面的截图中，我们在消费者等待超时的时候杀死了它。从而模拟任务之间的消费者崩溃。

消息回到队列中。

![](img/a7ff47ffc5ead92dde004405a69da013.png)

重新启动 worker，看它是否再次读取消息。

![](img/aa4548cedea86ae82d1e5dd691afa507.png)

如果我们的 RabbitMQ 实例崩溃了怎么办？我们如何确保有效载荷不丢失？

我们必须声明我们的队列是持久的，我们已经在 RabbitMQ 管理控制台中这样做了。其次，我们必须向生产者代码中的`sendToQueue`方法添加第三个参数。

```
{persistent: true}
```

所以这个函数看起来像:

```
export const publishToQueue = async (queueName, data) => {
  ch.sendToQueue(queueName, new Buffer(data), {persistent: true});
}
```

这是 RabbitMQ 的一个非常基本的用法。你可以用它做更多的事情。您可以运行多个消费者，并决定向他们发送消息的策略。您还可以使用 RabbitMQ 设计一个发布/订阅服务，以及基于主题的路由。

# 密码

[https://github.com/pankaj805/medium-08_mq](https://github.com/pankaj805/medium-08_mq)

https://github.com/pankaj805/medium-08_mq_workers

如果你喜欢这篇文章，请评论并与他人分享。你可以在这里找到整个[系列文章](https://medium.com/@pankaj.panigrahi/list-of-node-js-articles-ededa6dd304b)。