# 在 iOS Swift 应用程序中设计一个分析或事件跟踪管理器

> 原文：<https://betterprogramming.pub/design-an-analytics-or-event-tracking-manager-for-an-ios-app-75979e7144ee>

## 实施分析包装器，以支持多个 SDK 并跟踪应用中的事件

![](img/1d2cb8c53f8b21debf72e928cdad1843.png)

卢克·切瑟在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*“信息是 21 世纪的石油，分析是内燃机”* — *Peter Sondergaard*

# **为什么需要数据分析？**

*   衡量和跟踪你的工作。
*   了解你的访客，线索，前景。
*   了解、跟踪和改进将你的第一个访客转化为有价值客户的机制。
*   主要目标是提高认知度、参与度和忠诚度。

# 特征

作为一名开发人员，在设计灵活的分析引擎时，您可能需要考虑很多问题。其中包括:

*   支持多个分析 SDK
*   灵活添加、更新和删除分析 SDK
*   添加、更新和删除灵活的事件及其属性
*   将事件分组以提高可读性
*   一个可测试和可模仿的模块
*   灵活地在应用内的任何地方使用它来跟踪事件。

我确信还有很多地方需要补充，但这些是开发者在设计分析层时需要考虑的一些理想的东西，而不违反最佳实践。

# 事件

我们将从一个非常基本的`EventProtocol`开始，它将理想地陈述我们需要在一个事件中发送哪些属性:

```
protocol EventProtocol {                           
  var name: String { get set }                           
  var params: [String: Any] { get set }                       
}
```

然后我们将创建实现我们的`EventProtocol` *的`Event`结构。*我们也将利用`propertyWrapper`进行建筑活动。

对于这个例子，我还在`Event`扩展中添加了一些助手函数:

# 模拟事件

在这个例子中，我们将发送两种事件:屏幕访问和点击事件。

为了方便起见，我们创建了两个枚举，在其中我们将添加所有的事件和参数:

现在，这是有趣的部分。当我们在开发一个巨大的应用程序时，我们必须从应用程序的不同部分发送事件。有许多方法可以对接所有这些事件，比如我们添加所有事件的单枚举、结构或类方法。但是当这个枚举、结构或类增长到 500 多个事件时，它就失去了可读性。

假设您的经理或主管向您询问任何功能的活动信息。很难找到它，把它全部列在一个枚举、结构或类中也会让你的代码看起来很松散。

解决方案是借助`propertyWrapper` **将我们的事件按特征分组。你可以看到我们创建事件是多么容易，我们的代码可读性更强，任何人都容易理解。**

# 让我们实现记录器

我们将首先创建`AnalyticsEventsLoggerProtocol`、，它将陈述任何分析引擎(SDK)的基本要求，例如:

*   初始化和设置
*   事件跟踪

```
protocol AnalyticsEventsLoggerProtocol { 
  func setUserProperties(user: UserProfileModel) 
  func trackEvent(event: EventProtocol) 
}
```

现在，对我们来说，初始化任何分析 SDK 都很简单。下面是 Firebase SDK 的一个示例:

```
// Firebase 
class FirebaseEventLogger: AnalyticsEventsLoggerProtocol { 
   func setUserProperties(user: UserProfileModel) { 
     // init SDK and set user properties 
   }    func trackEvent(event: EventProtocol) { 
     // add implementation of tracking events 
   } 
}
```

# 让我们实现一个分析管理器

现在让我们来看看我们的老板，他将把事件路由到 analytics SDK 的引擎并发送事件。

我们将从创建`AnalyticsManagerProtocol` **、**开始，在这里我们将陈述它发送事件和设置的基本要求:

```
protocol AnalyticsManagerProtocol { 
   func setUp()
   func trackEvent(_ event: EventProtocol, params: [String: Any]) 
}
```

然后我们将这个协议实现到`AnalyticsManager` 类中。

这是一个单独的类，将持有所有分析引擎的数组引用，它将负责发送与`trackEvent` 协议方法的事件。您可以检查这个函数的实现，其中它调用它的 logger `trackEvent`方法来发送事件以跟踪用户操作:

# 总结

现在，只需拨打一条线路，即可在我们应用的任何部分发送事件，并发送事件:

```
// Without Params
AnalyticsManager.shared.trackEvent(FeatureOneEvents.screen)// With Params
AnalyticsManager.shared.trackEvent(FeatureOneEvents.screen,
                                  params:["source": "Tabs"])
```

# 谢谢你

我希望这篇文章能帮助你设计你自己的分析层。请在评论区分享您的反馈。

# 参考文献/好读物

*   贵族法典
*   约翰·桑德尔
*   [戴维德龙](https://davedelong.com/blog/2017/12/07/misusing-enums/)
*   伯努瓦·帕斯奎尔
*   [马特迪夫斯](https://matt.diephouse.com/2017/12/when-not-to-use-an-enum/)