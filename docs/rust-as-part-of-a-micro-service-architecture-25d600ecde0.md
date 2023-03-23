# 铁锈是微服务架构的一部分

> 原文：<https://betterprogramming.pub/rust-as-part-of-a-micro-service-architecture-25d600ecde0>

## Rust +打字稿

![](img/63397820e3b0a9b13700a6fefb298ed1.png)

早在一段时间前，我已经写过一篇关于如何在全栈 web 应用中使用 Rust 的小文章(参见[这里](/using-the-diesel-orm-for-a-web-app-with-rocket-90e610f6a6cf))。这篇文章旨在通过提供一种替代方法来将 Rust 包含到应用程序中，从而对此进行跟进。

一般来说，我们可能会说:

Rust 最明显的优势是解决 CPU 密集型任务的速度和非常高效的内存处理。后者不需要任何垃圾收集器。

尽管这些特性很好，但它们也有一点缺点——它要求我们坚持非常严格的所有权模型。但是，并不总是有人认为这是一个缺点，因为坚持所有权模型会产生非常稳定和可维护的代码。

然而，有时候开发速度是项目中的一个重要因素。特别是，希望将应用程序分成几个部分，并为每个部分使用最合适的语言。实现这个概念的一个解决方案是使用微服务架构，这正是我们在这个故事中要讨论的。

理解本文内容的先决条件是 Rust 和 TypeScript 的基础知识。两者都可以分别从[这里](https://medium.com/@applied-math-coding/list/an-introduction-into-rust-22c99777c5e5)获得。[此处](https://medium.com/@applied-math-coding/list/a-series-in-typescript-6e07e28de652)。

# 总体目标

为了有一个简单的例子，我们将实现三个微服务:

1.  这提供了一个公共 API 并托管了一个小型的基于 Vue 的客户端。该语言是类型脚本，我们将使用非常流行的框架 [NestJS](https://docs.nestjs.com/) 。
2.  `calc-engine`:这是一个 Rust 服务器，提供一些方法来完成一些 CPU 密集型的计算。
3.  `rabbitmq`:它被认为是上述服务之间的消息代理，由 [RabbitMQ](https://www.rabbitmq.com/) 提供支持。

最后，所有这些都将部署在 docker-compose 中，使其易于共享。

在下文中，我将只对实现的最基本部分进行描述，以提供尽可能愉快的阅读体验。完整的代码可以在这个[仓库](https://github.com/applied-math-coding/rust-node-rabbit)中查看，该仓库仅用于教育目的。

# 计算引擎(生锈)

这部分是一个典型的基于货物的二进制应用程序。依赖关系如下:

```
**amiquip** = { version = "0.4.2", default-features = false }
serde_json = { version = "1.0.81" }
rayon = { version = "1.5.3" }
num_cpus = { version = "1.13.1" }
dotenv = { version = "0.15.0" }
```

最重要的一个是`amiquip`，它支持与 RabbitMQ 的通信。crate `rayon`用于使应用程序使用尽可能多的并行计算。特别是，我们设置了一个线程池，如下所示:

```
fn setup_pool() -> rayon::ThreadPool {
    rayon::ThreadPoolBuilder::new()
        .num_threads(num_cpus::get()) 
        .build()
        .unwrap()
}
```

对于 CPU 密集型任务，并行运行的 CPU 数量不超过可用的 CPU 数量通常是有意义的。

到 RabbitMQ 的连接是这样获得的:

```
fn setup_connection() -> Connection {
    if let Ok(c) = Connection::insecure_open(&format!(
        "amqp://{}:{}@{}:{}",
        env::var("RABBITMQ_USER").unwrap(),
        env::var("RABBITMQ_PWD").unwrap(),
        env::var("RABBITMQ_HOST").unwrap(),
        env::var("RABBITMQ_PORT").unwrap()
    )) {
        println!("Connected to rabbitmq!");
        c
    } else {
        println!("Failed to connect to rabbitmq. Will retry in 2s.");
        std::thread::sleep(std::time::Duration::from_secs(2));
        setup_connection()
    }
}
```

除了从`.env`文件中获取某些值的传统方法之外，我们还通过每隔`2s`重试来应对连接失败。这种方法特别适合微服务设置，因为每个涉及的服务都应该能够从其他服务的临时故障中恢复。

作为服务之间的通信方式，我们将使用`RPC`(远程过程调用)。这并不总是最合适的，但在这里已经足够了。我们“繁重”的 CPU 任务将是计算河内[塔](https://applied-math-coding.medium.com/solving-the-tower-of-hanoi-in-rust-243e00e6ac34)的求解步骤。这将在下面解释命名。

我们在这里做的第一件事是确保 RabbitMQ 上存在名为`hanoi`的队列。这是通过打开`connection`上的`channel`，然后声明`queue`来完成的。

这个`queue`被用作跨服务的消息生产/消费。我们可以通过调用`queue.receiver()`来监听放在`queue`上的消息。这类似于监听 Rust 的内部[通道](https://applied-math-coding.medium.com/an-introduction-into-rust-part-14-multi-threading-7bbac4cb0e2f)并确保代码块中的代码一条消息一条消息地运行。

每个新的`message`必须与其类型匹配，原则上是`ConsumerMessage::Delivery`或我们在`other`中共同处理的一些连接错误。除了`body`之外，该消息还有另外两个字段，即`reply_to`和`correlation_id`。`body`表示实际内容(这里是河内游戏中元素的数量)，`reply_to`是用于发回结果的“独占”队列，`correlation_id`用于匹配请求和响应。

因此，向`hanoi`队列发送消息的客户端会附加一个惟一的`correlation_id`,使其能够在以后选择由服务器写入`reply_to`队列的结果。为了并行处理尽可能多的 CPU 任务，我们将当前请求放在线程池的一个闭包中。每个这样的闭包都包含通过`reply_to`队列发回结果的逻辑。

发送是通过使用`channel_for_msg`通道的`Exchange::direct`实例来完成的。`other`的处理方式是每隔`2s`重新尝试建立这个监听器构造。同样，这使得我们的服务不受连接失败或 RabbitMQ 重启的影响。

# 主服务器(类型脚本)

首先，主服务器提供了一个公共 API，让客户端请求 Hanoi 游戏的求解步骤:

```
[@Get](http://twitter.com/Get)('/hanoi')
getHello([@Query](http://twitter.com/Query)('n', ParseIntPipe) n: number): Promise<string> {
  ...
  return this.appService.makeHanoi(n);
}
```

如果你从未接触过 NestJS，没问题，这是学习 web 框架最简单的方法，在这个简单易懂的[概述](https://docs.nestjs.com/first-steps)中介绍了它的基本概念。

如您所见，端点正在委托一个方法`makeHanoi`，该方法最终在方法`MessageService.sendMessage`处结束。该服务包含连接到 RabbitMQ 的逻辑，但现在是从主服务器端连接:

```
async **sendMessage**(n: number): Promise<string> {
        const **channel** = await this.ensureChannel();
        const **replyTo** = await this.ensureResponseQueue(channel);
        const **correlationId** = this.generateUuid();
        channel.sendToQueue(
            this.HANOI_QUEUE,
            Buffer.from(`${n}`),
            {
                correlationId,
                replyTo
            });
        return lastValueFrom(
            this.queueResponse.pipe(
                filter(
                  m => m?.properties.correlationId === correlationId
                ),
                first(),
                map(m => m.content.toString())
            )
        );
}
```

在这里您可以识别所有以前预约的消息参数，即`replyTo`和`correlationId`。因为我们在生产者端，所以这些值在生产者端产生，并附加到发送到`hanoi`队列的消息上。

在发送到`hanoi`队列后，我们在队列上注册了一个一次性监听器，用作`replyTo`。更详细地说，这个后面的队列在内部被使用，以向下面的`BehaviorSubject`发出值:

```
private queueResponse = new BehaviorSubject<Message>(null);
```

如您所见，上面的代码在第一个匹配的`correlationId`上注册了一个订阅，并通过使用操作符`lastValueFrom`将其转换为`Promise`。

如果你从未见过这样的东西，这是基于流行的反应库 [rxjs](https://rxjs.dev/) 。

在不涉及所有细节的情况下，让我们简短地看一下`ensureChannel`的实现。

实际上，这段代码的核心部分很简单:`connect(...)`建立一个`connection`到 RabbitMQ，`connection.createChannel`使用这个连接创建一个`channel`断言队列`hanoi`存在。

围绕这个方法的所有混乱都是必要的，以确保这个方法在其运行的异步上下文中正确工作。建立的`channel`存储在本地字段中，不必在每次发送消息时重新创建。

# 客户

此外，主服务器还托管一个由 [Vue.js](https://vuejs.org/) 支持的小型客户端应用程序。如果您对它的实现感兴趣，您可以在前面提到的[库](https://github.com/applied-math-coding/rust-node-rabbit)中找到所有代码。它的目的是提供以下简单的用户界面:

![](img/35acf73b56364d68e0ea5577f48f5485.png)

## docker-撰写:

所有上述组件被构建并一起放在一个 [docker-compose](https://docs.docker.com/compose/) 容器中。以下是对应`docker-compose.yml`的内容:

```
version: "3"
services:
  **main-server**:
    build: ./main-server
    env_file: .env
    environment:
      - RABBITMQ_HOST=rabbitmq
    ports:
      - "3000:3000"**calc-engine**:
    build: ./calc-engine
    env_file: .env
    environment:
      - RABBITMQ_HOST=rabbitmq**rabbitmq**:
    image: rabbitmq:3-management
```

如果您克隆了存储库并在系统上安装了 docker，那么您可以通过从位于存储库根文件夹中的终端发出以下命令来运行代码:

```
> docker-compose up
```

之后，您可以将浏览器指向`[http://localhost:3000](http://localhost:3000)`来“享受”上面的 UI。

# 结论

这并不是将一个应用程序的不同部分分成几个编译单元的唯一方法。在 Node.js 的上下文中，一个非常有前途的方法是使用 [NAPI-RS](https://napi.rs/) 。

这避免了消息的开销，你可以在这里阅读更多关于这个[的内容。另一方面，通过将不同的部分放入独立的服务器(微服务器？)您将获得与众不同的可扩展性。因此，在集群中运行时，每个部分都可以根据需要进行扩展或完成。](https://applied-math-coding.medium.com/using-rust-from-within-node-js-or-extending-node-js-via-n-api-using-napi-rs-e022c1753cb5)

感谢阅读！