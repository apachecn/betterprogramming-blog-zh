# 什么是装饰设计模式？

> 原文：<https://betterprogramming.pub/decorator-c04fae63dfff>

## Decorator 是一种有用的模式，它将使向代码添加新功能变得更加容易

![](img/32e9ae39a8c81b34b0c3890c32e8b27f.png)

继承使你的类和它的基类永久耦合。有时，您希望动态地附加新的行为或功能，而不必创建额外的层次结构。

你可以通过用一个已经有你想要的行为的新的包装它来达到这个目的。它就像套娃一样。对于客户端来说，它看起来是一样的，因为它具有相同的界面，但可以具有额外的功能。

让我们想象一下，有一家商店出售不同的消费品。每个产品都有名字和价格。

每件衬衫都有一个名称和价格，计算方法是将基本价格乘以服装的增值税(VAT)率。

电视的名称略有不同，增值税率也更高。

为了促进销售，你想组织季节性促销活动。

产品应该有更吸引人的描述和打折的价格。一种方法是扩展每个产品类并覆盖现有的属性。

然而，这有一个很大的缺点。如果您计划进行两次季节性销售，您必须为两种产品创建四个额外的类。如果有五种产品，你需要 10 种。对于这样一个简单的用例来说，这肯定太多了。

如果您决定使用 Decorator 设计模式，您可以为每个季节性计划使用一个单独的类来包装产品并共享相同的接口。

让我们进行夏季和冬季销售。每个产品都会有一个折扣价格和一个增强的描述。

夏季销售将有更积极的价格政策和不同的营销口号。

现在我们已经设置好了，让我们开始工作吧。

综上所述，Decorator 有以下元素:

*   Component ( `Product`):定义一个可以被修饰的元素的接口
*   具体组件(`TV`、`Shirt`):组件的实现
*   Decorator ( `ProductDecorator`):定义 Decorator 的接口
*   混凝土装饰器(`SummerSale`，`WinterSale`):装饰器的实现

# 优势

*   你可以在一个类中使用多个装饰器。在 PHP 中，这种行为不能通过继承来实现，因为它不支持扩展多个类
*   附加新行为可以是动态的，并在运行时决定
*   这种责任不同的班级是独立的

# 源代码

完整的源代码和其他一些模式可以在这里找到:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### 这个项目是一组在现实世界中使用不同设计模式的简单例子。每个人都有一个…

github.com](https://github.com/jkapuscik2/design-patterns-php) 

如果您想了解一些更有用的设计模式，您可以在以下文章中找到它们:

*   [工厂方法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
*   [创作模式](https://medium.com/@j.kapuscik2/creational-design-patterns-in-php-db365d3245ce)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&策略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [模板方法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [飞锤](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [复合](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [立面](https://medium.com/better-programming/what-is-facade-design-pattern-67cb09ce35d4)
*   [桥](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [来访者](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)