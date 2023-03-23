# 什么是空对象设计模式？

> 原文：<https://betterprogramming.pub/what-is-null-object-design-pattern-f3b4d3d28636>

## 移除不必要的条件复杂性

![](img/10365bc223f18460bee800ea1f6e8e36.png)

照片由 [Monika Dhita Adiati](https://unsplash.com/@dhitaadiati?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/empty?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

通常情况下，我们必须在使用变量之前检查它是否为空。如果我们从数据库中查询一个模型，结果可能是什么也没有。因此，我们必须添加一个额外的`if`来检查情况是否如此。

空对象模式引入了另一种解决方案。我们可以使用一个虚拟对象，它与原始对象共享相同的干涉，但不实现任何行为。

我们的代码变得更干净，我们可以删除“如果”。“如果”越少越好。

如果具有给定名称的产品存在，我们将收到一个正确的模型。否则，将返回 null。通过引入一个没有行为的`NullProduct`，我们可以简化我们的应用程序。

`NullProduct`将只引入默认行为，而不引入底层逻辑。如果没有找到模型，将由`ProductDAO`返回。因此，`getProductByName`方法的返回类型将总是类型`Product`。

如果有些对象什么也做不了，在很多情况下会好得多。大多数时候，我们不希望在本地机器上开发时使用缓存。这真的很令人沮丧。

同时，我们需要确保我们的软件可以被缓存，并且在生产中一切正常。

这样的问题也可以通过提供高速缓存组件的虚拟实现来解决，该虚拟实现什么也不做，并且可以容易地用真实实现来替换。

我们可能有不同的实现将缓存存储在 [MySQL](https://www.mysql.com/) 、 [Redis](https://redis.io/) 、 [Memcached](https://memcached.org/) 、内存或任何其他可能的地方。

内存缓存的示例实现

如果我们在本地开发应用程序，我们所要做的就是配置缓存来使用我们的虚拟实现。

# 优势

*   可以简化代码并消除条件复杂性。
*   在开发过程中会很有帮助。
*   简化客户界面。

# 不足之处

*   如果实现不当，可能会隐藏错误并产生意外行为。
*   会使应用程序过于复杂。

# 源代码

完整的源代码和其他一些模式可以在这里找到:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### PHP 中设计模式的例子。通过在…上创建帐户，为 jkapuscik2/design-patterns-php 开发做出贡献

github.com](https://github.com/jkapuscik2/design-patterns-php) 

# 参考

*   罗伯特·c·马丁， [*敏捷软件开发:原则、模式和实践*](https://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445) *。*

如果您想了解更多的设计模式，您可以在以下文章中找到它们:

*   [工厂方法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
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
*   [桥](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [访客](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)