# Ruby 中配置驱动的反应式编程

> 原文：<https://betterprogramming.pub/config-driven-reactive-programming-in-ruby-676863f50881>

## 初学者指南

![](img/4097a7d24800ff8ec5a59f36a02f0631.png)

梅勒妮·德泽尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

最近，我正在检查一个基于 Ruby on Rails 的遗留应用程序，我想知道是否有什么方法可以修改某些工作流来提高性能和可配置性。尽管我所遵循的方法在现有的 Ruby gems 中已经可用，但我发现以可配置的方式编写它是令人兴奋的。

> 问题陈述:订单管理系统有一个订单模型，一个订单实例可以附加一个状态。我们不使用任何状态机来调节订单状态的变化，因此我想开发我自己的可定制的状态机。

方法 **:** 我将利用 Ruby 中的 observer 模式来构建我的类。当对象之间存在一对多关系时，使用观察者模式，例如，如果一个对象被修改，它的依赖对象将被自动通知。观察者模式属于行为模式类别。

最初，我设置了一个散列常数，将发布者映射到订阅者。为了简单起见，我把它放在一个 ruby 文件中。在生产中，这可以存储在集中的配置管理器或数据库中，这样就可以配置这些值，并且可以实时看到变化。

接下来，我展示了一个`DeliveryService`，这是调用我们的发布者服务的服务。

`OrderStateService`是最重要的类，它充当我们的发布者。我们需要 Ruby 的 observer 模块，在第 8 行，我们通过类名- >过滤了`OBSERVER_CONFIG`，这将返回为`OrderStateService`发布者注册的所有订阅者。

在第 9 行中，我们遍历每个订阅者，并将他们注册为`OrderStateService`发布者的观察者。只有当观察者实现了更新方法时，才会执行此操作。[这是因为文档中指定通过 update 方法监听。](https://ruby-doc.org/stdlib-2.5.3/libdoc/observer/rdoc/Observable.html)

在第 17 行中，我们表示已经进行了更改，在第 18 行中，事件被传递给我们所有的订阅者。我们还通过使用 Ruby 的多线程功能来异步运行它。

作为订阅者的`NotificationService`实现了一个更新方法，当在`OrderStateService`中注册了一个变更事件时，它将执行任何操作。

如果需要创建一个新的订户，我们只需要用一个更新方法创建这个类，并在我们的`OBSERVER_MAPPING`配置中注册这个订户。

这只是一个原型，问题陈述的解决方案可以进一步改进，例如订单状态转换这样的事件可以让几个订阅者异步执行多个操作。由于这完全是配置驱动的，管理员可以在运行时轻松地添加/删除订户，这有助于隔离错误、性能监控等。

## 这种方法的优点

1.  开/关原则。您可以引入新的订阅者类，而不必更改发布者的代码(如果有发布者接口，反之亦然)。
2.  您可以在运行时建立对象之间的关系。
3.  您可以轻松地添加或删除订阅者，而无需进行任何代码更改或重新部署。

## 这种方法的缺点

1.  以随机顺序通知订户。
2.  如果没有设置适当的日志记录机制，可能很难调试。
3.  代码完全依赖于配置。应该建立单独的验证和安全机制，以确保配置不会被滥用。

## 参考

1.  Ruby 的 observable 模块—[https://ruby-doc . org/stdlib-2 . 5 . 3/lib doc/observer/rdoc/observable . html](https://ruby-doc.org/stdlib-2.5.3/libdoc/observer/rdoc/Observable.html)
2.  观察者设计模式—[https://refactoring.guru/design-patterns/observer](https://refactoring.guru/design-patterns/observer)
3.  Ruby 中的多线程技术—[https://www.rubyguides.com/2015/07/ruby-threads/](https://www.rubyguides.com/2015/07/ruby-threads/)