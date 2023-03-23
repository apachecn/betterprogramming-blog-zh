# 在 Swift 5 中实现外观设计模式

> 原文：<https://betterprogramming.pub/implement-the-facade-design-pattern-in-swift-dcc4325754ff>

## 使与复杂 API 的通信更加容易

![](img/8d274581438265eb25e3e68864072105.png)

照片由[Unsplash](https://unsplash.com/@all_bong?utm_source=medium&utm_medium=referral)上的 [All Bong](https://unsplash.com/@all_bong?utm_source=medium&utm_medium=referral) 拍摄。

在本教程中，我们将学习如何在 Swift 中实现外观设计模式。

Facade 允许我们通过隐藏内部实现细节并向客户端公开简单方法来简化与复杂 API 的交互方式。

该项目的源代码可以在文章的底部找到。

# 我们开始吧

考虑以下情况:我们正在开发一个应用程序，应用程序开发公司提供他们的服务，客户可以下订单。

我们先来定义一下`Project`类:

现在我们定义`Client`类，它有两个职责:创建订单建议和发送付款:

接下来是`ProductManager`,负责设定项目的截止日期，接受草图和最终设计，为开发者创建 Trello 板:

类似地，我们定义了`Designer`类:

还有那个`Developer`班:

最后，我们可以创建外观:

使用`OrderFacade`类，我们通过`placeOrder(project:)`方法公开了一个创建定制订单的简单接口:

# 资源

GitHub 上提供了源代码:

[](https://github.com/zafarivaev/FacadeDesignPattern) [## zafarivaev/FacadeDesignPattern

### Swift 中实施的外观设计模式的简单说明。-zafarivaev/FacadeDesignPattern

github.com](https://github.com/zafarivaev/FacadeDesignPattern) 

# 包扎

对其他设计模式感兴趣？请随意查看我的其他相关作品:

[](https://medium.com/better-programming/implement-the-adapter-design-pattern-in-swift-165ac22bc868) [## 在 Swift 中实施适配器设计模式

### 让不兼容的接口协同工作

medium.com](https://medium.com/better-programming/implement-the-adapter-design-pattern-in-swift-165ac22bc868) [](https://medium.com/better-programming/implement-the-builder-design-pattern-in-swift-5-ff5bc6f2fc3d) [## 在 Swift 5 中实施构建器设计模式

### 创建和使用自定义 URLRequests 和 URLSessionDataTasks

medium.com](https://medium.com/better-programming/implement-the-builder-design-pattern-in-swift-5-ff5bc6f2fc3d) [](https://medium.com/better-programming/implement-the-strategy-design-pattern-in-swift-5d9c3f221277) [## 在 Swift 中实施战略设计模式

### 只需一行代码就可以在领域和核心数据之间切换

medium.com](https://medium.com/better-programming/implement-the-strategy-design-pattern-in-swift-5d9c3f221277) [](https://medium.com/better-programming/this-is-how-you-implement-mvc-architecture-in-2020-b39d65b4212f) [## 新 MVC:单一责任原则和委托

### 苹果的模型视图控制器，终于做对了

medium.com](https://medium.com/better-programming/this-is-how-you-implement-mvc-architecture-in-2020-b39d65b4212f) [](https://medium.com/better-programming/implement-a-model-view-presenter-architecture-in-swift-5-dfa21bbb8e0b) [## 在 Swift 5 中实施模型-视图-演示者架构

### 使用领域数据库作为业务逻辑层

medium.com](https://medium.com/better-programming/implement-a-model-view-presenter-architecture-in-swift-5-dfa21bbb8e0b) [](https://medium.com/better-programming/reactive-mvvm-and-the-coordinator-pattern-done-right-88248baf8ca5) [## 反应式 MVVM 和协调模式做对了

### 使用 Swift 5、RxSwift 和 RxDataSources

medium.com](https://medium.com/better-programming/reactive-mvvm-and-the-coordinator-pattern-done-right-88248baf8ca5) [](https://medium.com/better-programming/how-to-implement-viper-architecture-in-your-ios-app-rest-api-and-kingfisher-f494a0891c43) [## 如何使用 Swift 5 在您的 iOS 应用中实现 VIPER 架构

### 使用方便的 Xcode 模板和参考报告

medium.com](https://medium.com/better-programming/how-to-implement-viper-architecture-in-your-ios-app-rest-api-and-kingfisher-f494a0891c43) [](https://medium.com/better-programming/implement-a-service-oriented-architecture-in-swift-5-fc70b8117616) [## 在 Swift 5 中实施面向服务的架构

### 为什么只用 MVC/MVVM/蝰蛇还不够

medium.com](https://medium.com/better-programming/implement-a-service-oriented-architecture-in-swift-5-fc70b8117616) 

感谢阅读！