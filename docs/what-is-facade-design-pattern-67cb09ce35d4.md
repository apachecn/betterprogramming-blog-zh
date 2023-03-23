# 什么是立面设计模式？

> 原文：<https://betterprogramming.pub/what-is-facade-design-pattern-67cb09ce35d4>

## Facade 帮助我们用最小的接口充分利用我们的第三方组件

![](img/4d01f2ae74e24f8b034f0c8938e8dd52.png)

资料来源:Unsplash。作者:[尼克拉斯·加恩霍尔茨](https://unsplash.com/@digitalfive)

在编程中，我们几乎总是要同时处理多个组件，并让它们一起工作。我们可能遇到的一些问题已经得到很好的解决，值得注意。

结构设计模式基本上显示了在给定的场景中如何组织对象关系。他们专注于以最灵活、最易操作的方式构建新的结构。

# 立面设计模式

facade 设计模式侧重于处理复杂的组件或类集。

我们的想法是通过一个只暴露我们需要的东西的极简界面将它们从客户端代码中分离出来。它应该负责与这些复杂结构的通信，并使它们尽可能容易地工作。

让我们开设一门课程，帮助我们处理图像。现在，我们想要做的就是能够创建一个简单的缩略图。我们可以使用 Imagick 或其他一些库直接做到这一点。唯一的问题是他们有一个比我们目前需要的更广泛的接口。

但是这对于 facade 来说是一个很好的用例，因为它只暴露了我们将要使用的最小接口。

现在，我们不必关心 Imagick 的内部 API。

如果我们想制作一个缩略图，我们所要做的就是提供一个到图像的路径和它想要的大小。

综上所述，门面有以下要素:

*   Facade ( `Image`)负责与客户端的交互，并将工作委托给更复杂的组件。它定义了与这些组件交互的接口。
*   子系统类(`Imagick`)是一个复杂的实体或一组实体，处理 Facade 分配的任务。那些实体不知道外观的存在，也不依赖它。

# 优势

*   使界面更整洁，更易于使用
*   封装内部实现

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
*   [模板法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [飞锤](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [装饰工](https://medium.com/better-programming/decorator-c04fae63dfff)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [复合](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [桥](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [访客](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)