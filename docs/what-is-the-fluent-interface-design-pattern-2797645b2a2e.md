# 什么是流畅的界面设计模式？

> 原文：<https://betterprogramming.pub/what-is-the-fluent-interface-design-pattern-2797645b2a2e>

## 提升你的类的接口

![](img/a80dfd76baab797fe3d52de242d5ba25.png)

由[米卡·查芬](https://unsplash.com/@micahgray?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fluid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片。

有些概念如此简单，以至于令人惊讶的是它们居然有一个名字。这并不意味着它们没有用。通常，最简单的概念对工作来说是最好的。流畅的接口简化了我们使用对象 API 的方式，并促进了方法链接。这是一种非常有用的技术，可以使类的接口更具表现力，更易于客户使用。

让我们考虑一个 PHP 中简单的 SQL 查询构建器的例子。我们可以创建带有可选条件的选择查询:

一切都很好，但不是很方便。稍加调整，我们就能让它使用起来更加舒适。现在，每个方法只负责添加查询的新部分，但是它也可以返回它的类实例并允许方法链接。我们的查询构建器的界面如下所示:

如果一个类的 API 被多次使用，那么使用这种方法可能会更容易、更透明。带有流体接口的`QueryBuilder`的示例实现如下所示:

注意:这只是一个例子，不是生产就绪代码。

# 源代码

完整的源代码和其他一些模式可以在这里找到:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### PHP 中设计模式的例子。通过在…上创建帐户，为 jkapuscik2/design-patterns-php 开发做出贡献

github.com](https://github.com/jkapuscik2/design-patterns-php) 

# 资源

[](https://martinfowler.com/bliki/FluentInterface.html) [## bliki: FluentInterface

### 几个月前，我和 Eric Evans 一起参加了一个研讨会，他谈到了一种界面风格，我们…

martinfowler.com](https://martinfowler.com/bliki/FluentInterface.html) 

如果您想了解更多的设计模式，您可以在以下文章中找到它们:

*   [工厂方法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
*   [创作模式](https://medium.com/@j.kapuscik2/creational-design-patterns-in-php-db365d3245ce)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&策略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [模板方法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [飞锤](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [装潢师](https://medium.com/better-programming/decorator-c04fae63dfff)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [复合](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [立面](https://medium.com/better-programming/what-is-facade-design-pattern-67cb09ce35d4)
*   [桥](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [访客](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)