# 使用发布-订阅模式水平扩展 WebSocket 服务器的设计考虑

> 原文：<https://betterprogramming.pub/design-considerations-for-scaling-websocket-server-horizontally-with-a-publish-subscribe-pattern-fe6de9988400>

## 了解扩展 WebSocket 服务器的挑战

![](img/e48110b449243735c18f0bd2e7147ab6.png)

凯利·西克玛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在我的[上一篇文章](https://medium.com/@kbryan1008/building-a-websocket-server-in-a-microservice-architecture-50c6c6432e2b)中，我写了在微服务架构中设计和构建一个 WebSocket 服务器。尽管该实现对于 WebSocket 服务器的单个实例来说工作得很好，但是当我们试图扩大 WebSocket 服务器实例的数量(也称为水平扩展)时，我们将开始面临问题。本文研究了使用发布-订阅消息传递模式扩展 WebSocket 服务器的设计考虑。

# **我的 Websocket** 服务器**系列**

*   01: [在微服务架构中构建 WebSocket 服务器](https://medium.com/@kbryan1008/building-a-websocket-server-in-a-microservice-architecture-50c6c6432e2b)
*   02:使用发布-订阅模式水平扩展 WebSocket 服务器的设计考虑
*   03: [使用 Spring Boot、Redis 发布/订阅和 Redis 流实现可伸缩的 WebSocket 服务器](/implement-a-scalable-websocket-server-with-spring-boot-redis-pub-sub-and-redis-streams-b6b8cc08767f)
*   04:待定

# 什么是水平缩放？

首先，让我们试着理解为什么我们需要水平缩放。随着我们用户群的增长，服务器上的负载也在增长。当负载增加时，单个服务器将无法为所有用户提供高性能。因此，作为我们设计考虑的一部分，有必要提供随时增加/减少服务器数量的能力，以满足用户需求并节省资源。

水平扩展是指在基础设施中添加更多的机器，以应对服务器的高需求。在我们的微服务环境中，水平扩展等同于部署更多的微服务实例。然后需要一个负载平衡器在多个微服务实例之间分配流量，如下所示:

![](img/a0ea7be9874eff47c70d769a8acf5235.png)

使用负载平衡器进行水平扩展的示例

至此，我希望您能更好地理解为什么我们的基础设施需要水平扩展。因此，让我们继续学习在微服务架构中扩展 WebSocket 服务器的设计考虑。

# 快速回顾

![](img/2aa0674fd5c786d927ed15859450cc02.png)

微服务架构中 WebSocket 服务器的高级图

以前，我们使用 Spring Boot、Stomp 和 Redis Pub/Sub 实现了 WebSocket 服务器。web 应用程序(前端)和 WebSocket 服务器之间的通信是通过 WebSocket 进行的，而微服务(后端)和 WebSocket 服务器之间的通信是通过 API 和发布-订阅消息模式进行的。有关更多信息，请参考[上一篇文章](https://medium.com/@kbryan1008/building-a-websocket-server-in-a-microservice-architecture-50c6c6432e2b)。

# 有哪些问题和解决方案？

之前的设计在每个微服务只有一个实例的设置中运行得非常好。但是，在生产环境中拥有单个实例是不实际的。通常，我们将部署带有多个副本(或实例)的微服务，以在生产环境中实现高可用性。因此，当我们试图横向扩展 WebSocket 服务器(微服务)或后端微服务的数量时，我们会注意到以下问题。

## **问题#1:负载均衡器导致的消息丢失**

在我们的上一篇文章中，我们为后端微服务添加了 API，以便向 WebSocket 服务器发送消息，进行单向实时通信。如下所示，当扩展 WebSocket 服务器的数量时，负载平衡器有助于处理流量重定向。

![](img/702680054253c706d750ca3500455e16.png)

通过 API 从微服务(后端)向 web 应用程序(前端)发送消息时出现问题

在上面的设置中，web 应用程序的一个实例(前端)建立到 WebSocket 服务器的 WebSocket 连接(实例 B)。当后端服务器试图向 web 应用程序发送消息时，负载平衡器将 API 请求重定向到 WebSocket 服务器(实例 A)。因为 WebSocket 服务器(实例 A)没有到 web 应用程序的特定实例的 WebSocket 连接，所以消息将会丢失。

## **问题#1 的解决方案:使用发布/订阅广播消息**

![](img/5d0e4d68f3b64b22f8835cb9626b65a8.png)

负载均衡器导致消息丢失的解决方案

***注:*** *这个解决方案很大程度上受到了*[在我们之前的文章中，我们使用 Redis Pub/Sub 来处理 WebSocket 服务器(微服务)和后端微服务之间的双向实时通信。当我们增加 WebSocket 服务器和后端微服务的数量时，您会注意到 Redis Pub/Sub 的所有订户都会收到如下所示的消息。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[![](img/1459dc626cf317609b46085276b28839.png)](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[web 应用(前端)和微服务(后端)之间的双向实时通信](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[让我们看看双向实时通信中每个方向的消息流。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

*   [消息流:微服务到 web 应用程序(无重复处理)→WebSocket 服务器的所有实例都必须接收消息，因为每个 web 浏览器只与一个 WebSocket 服务器实例建立 web socket 连接。因此，当消息从后端微服务流向 web 应用程序时(后端→ WebSocket 服务器→前端)，只有 web 应用程序的一个实例会收到消息，这是正确的行为。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)
*   [消息流:web 应用程序到微服务(重复处理)→当消息从 Web 应用程序流向后端微服务(前端→ WebSocket 服务器→后端)时，我们预计只有一个后端微服务实例来处理消息。但是，所有后端微服务(作为订阅者)都会收到消息，导致消息被多次处理，这是不正确的行为。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

## [**问题#2 的解决方案:针对消费者群体的发布/订阅**](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[![](img/fb9b393295afe6befb90b8e53c783ed9.png)](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[一个主题有多个后端订阅者导致的重复消息处理的解决方案](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[为了解决第二个问题，我们将利用消费者群体的概念(由 Kafka 引入)，其中只有一个订户接收消息进行处理。这确保了不会有重复的消息处理，因为只有一个后端微服务实例将接收消息。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[由于我在上一篇文章《实现》中的 Redis Pub/Sub 不支持消费者组概念，我们可以使用 Redis Streams、Google Pub/Sub、RabbitMQ 或 Apache Kafka 来实现消费者组的发布-订阅消息传递模式。我不会详细讨论哪种方法更适合您的实现，因为这不是本文的目的。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

# [摘要](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[![](img/54f63601260a1a6c4b3336bc1019c7d5.png)](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[使用发布-订阅模式在微服务架构中扩展 WebSocket 服务器的完整设计](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[最后，我们讨论了如何在微服务架构中水平扩展 WebSocket 服务器的设计考虑。本质上，我们使用发布-订阅消息传递模式来确保在 web 应用程序(前端)和微服务(后端)之间的实时通信过程中没有消息丢失或重复的消息处理。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[就是这样！我希望你能从这篇文章中学到一些新的东西。本文只讨论了扩展 WebSocket 服务器的设计考虑。请继续关注下一篇文章，我将详细阐述如何使用 Redis 发布/订阅、Redis 流和 Spring Boot 来实现这种设计。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[如果你喜欢这篇文章，请关注我获取更多内容:)。](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[谢谢你一直读到最后。快乐学习！](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)

[](https://medium.com/u/a0e9fafab843#2:由于多个后端订阅者对一个主题进行重复的消息处理</strong></h2><p id=)