# 什么是责任链设计模式？

> 原文：<https://betterprogramming.pub/what-is-chain-of-responsibility-design-pattern-ff4d22abd124>

## 将发送方与接收方分离，使您的代码更加灵活

![](img/a26268ae1d41d861f98a18afb97c3249.png)

何塞·丰塔诺在 [Unsplash](https://unsplash.com/s/photos/chain?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

责任链使得请求的发送者独立于接收者。请求可以传递给多个连续的类，这些类可以处理或验证数据，并将其进一步传递给最终的处理程序。

每个处理程序都有定制的逻辑，但是共享一个公共接口，所以处理程序可以互换使用，它们的数量和顺序可以在运行时定义。

使用责任链的一个常见例子是一组处理传入 HTTP 请求的请求中间件。根据配置的不同，我们可能会检查用户的凭证、HTTP 头或请求体。

有些路由可能是公开的，我们将只使用基本验证，但有些路由应该只对管理员可用，并经过彻底验证。

责任链给了我们很大的灵活性，如果我们的请求不满足其中一个需求，它允许快速失败。让我们看看这样的场景是如何实现的。

我们正在创建一个微型 API，它将只处理`GET`请求并检查用户的 API 密钥。

API 验证是一个更复杂的任务，因为它可能需要数据库查询，所以我们将从一个简单的 HTTP 动词检查开始，只有当它成功时，我们才会进一步进行。

每个中间件必须共享相同的接口。它由数据处理和设置可选后继者的方法组成。

我们正在处理通过`Request`公开的数据。它允许访问来自`$_GET`和`$_SERVER`超级全球的数据。在现实生活中，我们不应该直接操作它们，但是为了简单起见，我们将省略任何数据清理。

`GetMiddleware`将使用关于收到的请求类型的数据。如果我们收到的不是`GET`，我们应该抛出一个错误并中断执行。

为了检查用户是否发送了正确的 API 密钥，我们需要从`$_GET`访问数据。为了简单起见，允许的键被硬编码到类中，但是在实际的应用程序中，我们将为此使用外部数据源。

我们的中间件已经准备好了，所以现在我们必须把它们放在一起，看看结果。所有请求都将由`App`处理，如果验证成功，客户端将收到样本响应。

`Response`显示短消息，以 JSON 的形式发送`GET`参数。

这是一个非常简单的例子，但是可以看出添加或删除中间件非常简单。

每一个都是完全独立的，可以单独开发和使用。责任链可能是一个非常强大的工具，有助于使应用程序更加灵活，耦合性更低。

# 想用就用吧

*   将发送方和接收方分离。
*   对数据处理或验证使用动态规则。
*   以特定的顺序处理请求。

# 优势

*   控制处理顺序。如果一些简单的规则没有得到满足，我们可以很快失败，并避免更复杂的检查。
*   分离每个处理者的责任更容易(单一责任原则)。
*   在不修改现有规则的情况下添加新规则更容易(开放/封闭原则)。

# 不足之处

*   它会使应用程序过于复杂。它不应与简单逻辑或单个处理程序一起使用。
*   在某些情况下，调试可能会更加困难。

# 源代码

完整的源代码和其他一些模式可以在这里找到:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### 这个项目是一组在现实世界中使用不同设计模式的简单例子。每个人都有一个…

github.com](https://github.com/jkapuscik2/design-patterns-php) 

如果您想了解一些更有用的设计模式，您可以在以下文章中找到它们:

*   [工厂法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
*   [创作模式](https://medium.com/@j.kapuscik2/creational-design-patterns-in-php-db365d3245ce)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&策略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [模板方法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [飞锤](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [装饰工](https://medium.com/better-programming/decorator-c04fae63dfff)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [复合](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [立面](https://medium.com/better-programming/what-is-facade-design-pattern-67cb09ce35d4)
*   [桥梁](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [访客](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)