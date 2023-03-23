# 从属倒置原则

> 原文：<https://betterprogramming.pub/solid-swift-by-examples-part-five-a860b86b85f5>

## Swift 实例中的坚实原则

## 坚固的雨燕

![](img/8a118e25627a1ef77787cef3b03c6637.png)

由[paweczerwi324ski](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

这是坚实的原则系列的最后一部分。在以前的文章中，我们讨论了什么是可靠原则，它们试图解决什么，以及什么是 SRP(I)、OCP(II)、LSP(III)和 ISP(IV)。

你可以在这里看到以前的原则:

[](https://medium.com/@piero9212/solid-swift-by-examples-part-one-35018d53d3e6) [## 坚固的雨燕

### 完整指南第一部分

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-one-35018d53d3e6) [](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) [## 开闭原理

### 在第一部分，我们描述了什么是坚实的原则和他们试图解决什么，解释了第一…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) [](https://medium.com/@piero9212/solid-swift-by-examples-part-three-675672c1ec20) [## 利斯科夫替代原理

### 这是关于坚实原理的出版物的第三部分。在以前的文章中，我们讨论了什么是固体…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-three-675672c1ec20) [](https://medium.com/@piero9212/solid-swift-by-examples-part-four-ec31bdb2872) [## 界面分离原理

### 坚实的原则通过 swift 中的例子坚实的 Swift 这是关于坚实的原则的出版物的第四部分。在…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-four-ec31bdb2872) 

在这一部分中，我们将探讨以下原理(DI 或 DIP):

依赖倒置从 90 年代开始流行，但是当 Robert C. Martin(著名的 Bob 叔叔)假定它是面向对象编程的一个伟大原则时，它变得更加流行。

他将其定义为两个部分:

> “高层模块不应该依赖低层模块。两者都应该依赖于抽象。”
> 
> “抽象不应该依赖于细节。细节应该依赖于抽象。”

首先，我们来定义一下什么是低级和高级模块。

*   一个*低级模块*是一段代码，它必须实现特定或具体的功能。例如，向我们的 API 发出请求的类、数据库类或特定的 UI 元素，如自定义地图。所有这些都提供特定的低级功能。
*   一个*高级模块*是一段拥有核心应用功能的代码，它可能需要一些低级模块。例如，一个处理登录用例的`LoginManager`类。

实现这一原则的关键是引入一个抽象(Swift 的协议)，由高层模块拥有，并反转对低层模块的依赖以符合该抽象。

这使得较低级别的模块“依赖”于较高级别的模块所要求的，实现了该原则的第一部分。

先说个例子。

让我们假设我们必须从一个 API 或数据库中获取用户信息，我们做了这样的事情:

用户照片服务

用户经理

代码正在工作，从我们的 API 中检索最新的用户照片。

现在，我们有了一个新的需求，一旦连接丢失，就用数据库请求替换当前的 API 请求。

用户照片服务

正如我们在前面的章节中了解到的，我们正在打破其他原则，例如单一责任和开闭原则。

让我们用抽象来适当地设计它，使用依赖倒置原则，看看我们如何能避免这种混乱。

首先让我们创建我们的抽象:

用户照片服务协议

为了实现这一抽象，我们创建了两个新的服务:

APIUserPhotoService

数据库用户照片服务

而且，我们保持我们的`UserManager`干净:

一个用户管理器，现在干净了

使用它，我们得到以下结果:

```
let apiService = ApiUserPhotoService()var userManager = UserManager(service: apiService)userManager.fetchUserPhoto(by: “123123”)//PRINTED RESULT **API REQUEST HERE**let dbService = DatabaseUserPhotoService()userManager.service = dbServiceuserManager.fetchUserPhoto(by: “12313”)//PRINTED RESULT **DB REQUEST HERE**
```

我们在这里做的是引入一个`UserPhotoService`抽象(协议)来定义`fetchUserPhoto(by, completionHandler)`方法。

注意，在两个服务的完成处理程序中，我们将照片作为`Data`发回，避免了每个服务类解析时的问题(API 响应和数据库响应可能会得到不同的响应类型)。

还有其他好处，比如如果我们收到一个请求，要将我们的数据库更改为一个[领域](https://realm.io/products/realm-database/)的数据库，我们只需要创建一个新的`RealmUserPhotoService`，从`UserPhotoService`开始实现。

使用这种方法，我们可以根据需要从我们的`UserManager`更改`UserPhotoService`，因此如果我们没有使用`APIUserPhotoService`的连接，我们可以简单地使用`DatabaseUserPhotoService`来代替。

当通过使用协议接口抽象将依赖关系相互隔离时，进行更改就变成了换入另一个插件。

# 结论

依赖是一种风险，我们通常在类中有很多依赖。根据这个原则，我们分离那些依赖关系，并为可能的变更请求做准备。

此外，开发新特性和测试可以是完全独立和有趣的，因为我们只需要实现一个`MockUserPhotoService`来测试我们的`UserManager`。

这一原则将指导你未来的应用程序架构设计决策，因为如果你的应用程序架构合理，即使第三方框架(如 Realm)关闭，你也可以很容易地用你想要的东西替换它。

记住，我们将面临三个常见的问题:脆弱、不动和僵硬，所以让我们运用我们坚实的原则来面对它们。

这些原则并不完美，这并不意味着我们将成为最伟大的开发者，但当你不得不做出决定时，它会产生影响。有了可重用和可维护的代码，我们的生活会变得更加有趣。

我希望您喜欢 SOLID 系列的最后一章，并发现它很有帮助。感谢阅读。

# 参考

[](https://martinfowler.com/articles/dipInTheWild.html) [## 在野外浸泡

### Brett L. Schuchert Brett 是一个多面手，努力做好最基本的事情。他总是在寻找新的…

martinfowler.com](https://martinfowler.com/articles/dipInTheWild.html)  [## 从属倒置原则

### 依存倒置原则(DIP)是其中一个坚实的原则。它涉及模块之间的关系…

medium.com](https://medium.com/@mglover/dependency-inversion-principle-c0264a405d57) [](https://codeburst.io/solid-design-principle-using-swift-fa67443672b8) [## 使用 Swift 的可靠设计原则

### 坚实的原则是实现/创建有效的、可维护的、可扩展的和松散的…

codeburst.io](https://codeburst.io/solid-design-principle-using-swift-fa67443672b8) [](https://medium.com/swift-india/solid-principles-part-5-dependency-inversion-principle-a0a890de71c2) [## 从属倒置原则

### 依赖性反转原理是解耦软件模块的一种特定形式。高级模块提供…

medium.com](https://medium.com/swift-india/solid-principles-part-5-dependency-inversion-principle-a0a890de71c2) [](https://medium.com/the-aesthetic-programmer/ios-solid-principles-pt-5-dependency-inversion-principle-70f25e86f259) [## iOS —固体原理第 5 部分—依赖性反转原理

### DIP——依赖性倒置原则，陈述如下:

medium.com](https://medium.com/the-aesthetic-programmer/ios-solid-principles-pt-5-dependency-inversion-principle-70f25e86f259)