# 如何在物联网项目中使用 Rust 和 MQTT

> 原文：<https://betterprogramming.pub/how-to-use-rust-and-mqtt-in-your-next-project-e4b80ad1b09e>

## 让我们看看 MQTT，以及在您的下一个物联网项目中，如何使用 rumqttc 机箱将 MQTT 与 Rust 结合使用。

![](img/c6936e71deebe651b5177679608b63b8.png)

照片由[安朵斯·瓦斯](https://unsplash.com/@wasdrew?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

你为什么想买物联网设备？在大多数情况下，你想获得更多的控制你的家。也许你只是想监控东西，甚至远程控制。

当然，你可以依赖制造商提供的应用程序。但是作为程序员，这不是你唯一的选择。也许，你想在洗衣机完成时收到一条信息，或者你想在设备耗电过多时收到警告。在这些情况下，可以看一看 MQTT。

在本文中，我们将在 Rust 中构建一个小型 MQTT 客户机，它监听 MQTT 代理并编写消息。所以让我们开始吧。

# 什么是 MQTT

那么首先，什么是 MQTT？MQTT 是一种轻量级、发布-订阅、机器对机器(M2M)的网络协议。它针对低带宽和高延迟进行了优化。

但是它是如何工作的呢？MQTT 按照发布-订阅模式工作。名为*发布者*的消息发送者不能直接指定名为*订阅者*的接收者，而是将发布的消息分类到名为*主题*的类别中。这在不知道有多少订阅者订阅了主题的情况下发生。但是这个系统在现实中是如何运作的呢？

为了使 MQTT 通信成为可能，我们需要一个代理和许多客户机。经纪人充当中介。客户端可以向代理上的主题发送消息。同时，其他客户端可以收听关于该主题的新消息。在我们阐明了这些基础知识之后，我们可以开始研究代码了。

对于这个小项目，我们将使用 Rust。Rust 是一种性能良好且可靠的语言。性能与 C++相当，同时通过其丰富的类型系统和所有权模型保证内存安全和线程安全。这两个特性在轻量级程序在后台长时间运行的特殊情况下都很有用。

# 生锈的 MQTT

对于本文，我们使用 Rust crate (=库)`[rumqttc](https://crates.io/crates/rumqttc)`。这是 Rust 中 MQTT 客户端的完整实现。

首先，我们需要创建一个新的 Rust 项目。

```
cargo new mqtt-rust-demo
```

切换到新项目后，我们将`rumqttc`添加到我们的`Cargo.toml`中。

```
rumqttc = "0.17.0"
```

安装完库之后，我们可以从代码开始。

# 客户

我们做的第一件事是用我们的连接数据创建一个变量。这是通过`MqttOptions`完成的。

```
let mut mqttoptions = MqttOptions::new("NAME", "YOUR BROKER", 1883);
mqttoptions.set_keep_alive(Duration::from_secs(5));
```

首先，我们需要指定客户端的名称。给客户机一个唯一的名称很重要，因为两个客户机在一个代理上不能有相同的名称。之后，代理的地址和端口将作为下一个参数跟在后面。您可以连接到互联网或本地网络上的代理。此外，您可以设置选项。在我们的例子中，客户端应该每五秒发送一条“仍然活动”的消息。

```
let (mut client, mut connection) = Client::new(mqttoptions, 10);
client.subscribe("demo/mqtt", QoS::AtMostOnce).unwrap();
```

这就是我们创建同步客户端的方式。我们用我们的选择来填充它。我们得到一个客户端对象和一个事件循环。后者以后会很重要。首先，我们说我们想要订阅主题`demo/mqtt`。

# 服务质量

此外，我们还指定了 QoS 级别。服务质量(QoS)是 MQTT 协议的一个关键特性。有 3 种不同级别的 QoS:最多一次(0)，至少一次(1)，正好一次(2)。

*   *QoS 0 —最多一次*只是向代理发送消息(就像在 *fire and forget* )一样。这个级别应该只用于非常可靠的网络
*   *QoS 1 —至少一次*向代理发送消息。同时，发送方在本地存储消息，直到代理发回确认消息(PUBACK 消息)为止。当一段时间后没有确认消息返回时，发送方重复该消息。
*   *QoS 2——恰好一次*向代理发送消息(这并不奇怪)。像 QoS 1 一样，它一直等到收到确认消息(`PUBREC`)才返回。在此之前，发件人会发出带有重复标志的相同的已发布邮件。当发送方收到确认消息时，它用一个`PUBREL`消息响应代理。与此同时，发送者存储了`PUBREC`消息。当代理接收到`PUBREL`消息时，它可以安全地丢弃该消息的所有临时存储的数据。同时，代理向发送者发送一个`PUBCOMP`消息。现在，发送者可以删除所有时态数据。

# 事件循环

在这个主题中，我们发送一些消息。所以我们产生了一个新的任务。在此任务中，我们发布一条带有主题编号的消息。

```
thread::spawn(move || for i in 0..10 {
   client.publish("demo/mqtt", QoS::AtLeastOnce, false, vec![i; i as usize]).unwrap();
   thread::sleep(Duration::from_millis(100));
});
```

接下来，我们来到我们的事件循环对象。我们将它包裹在一个无限循环中，因为它应该总是监听主题的消息。
这是通过命令`connection.iter().enumerate()`完成的。接下来，我们输出消息。

```
for (_i, message) in connection.iter().enumerate() {
        println!("Message= {:?}", message);
}
```

# 摘要

本文只是对 MQTT 这个庞大而有趣的主题的一个很小的介绍。但是让我们看看你学到了什么——你对 MQTT 的结构有了基本的了解，并在 Rust 中创建了一个可以发送和接收消息的小程序。这是一个很好的开始！

下面是这篇文章的[代码](https://gist.github.com/ngarske/cc5257fff416459f10fd88c422e173cb)

```
This article can also be found on my blog: 

[https://nikolas.blog/how-to-use-rust-and-mqtt-in-your-next-project/](https://nikolas.blog/how-to-use-rust-and-mqtt-in-your-next-project/)
```