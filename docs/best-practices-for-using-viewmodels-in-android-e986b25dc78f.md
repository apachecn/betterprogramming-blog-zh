# 在 Android 中使用视图模型的最佳实践

> 原文：<https://betterprogramming.pub/best-practices-for-using-viewmodels-in-android-e986b25dc78f>

## 如何正确设置和使用 Java 和 Kotlin 视图模型

![](img/6b8fe14d7e0d8e7e62e8875622b82a7c.png)

Christian Wiediger 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 为什么应该使用视图模型？

在开始使用`ViewModels`的最佳实践之前，我们应该问自己为什么我们应该使用`ViewModel`。答案很简单:关注点分离(SoC)。这使得应用程序可维护、可扩展和可测试。

这也可以通过演示者(MVP)来实现，但我更喜欢`ViewModels`(干净的建筑或 MVVM)，因为它们不会在用户切换配置(横向和纵向)时被瞬间破坏。这解决了许多传统问题，例如不执行昂贵的操作，如在配置更改时从服务器加载数据。

足球就是分而治之。理解 SoC 的一个最好的真实例子是在一个软件开发公司中有不同的部门(人力资源、财务、质量保证、工程等。).在我们的例子中，我们使用`ViewModels`从 UI 相关的代码中深入到业务逻辑和后台任务中。

在 Android 术语中，他们使活动和片段尽可能精简，导致很少或没有生命周期相关的问题。这被认为是最佳实践之一，因为活动和片段只不过是 Android 操作系统和应用程序之间的桥梁。这意味着操作系统可以在任何给定的时间销毁它们。

在本文中，我将讨论我在与`ViewModels`打交道的经验中看到的最佳实践和常见错误。

# 不要传递上下文

我见过许多开发人员将上下文传递给`ViewModel`函数，尽管使用`ViewModels`的主要目的是将繁重的任务与 UI 相关的代码分开。

通常，当开发人员试图创建类的对象(例如，一个共享的首选项或需要`ViewModel`中上下文的网络库)而不是使用像 Dagger2 或 Koin 这样的依赖机制时，就会发生这种类型的错误。

这个问题的解决方案是使用一个合适的依赖注入库(最好是未来几天的 Hilt)并通过`ViewModel`构造函数传递必要的依赖。

发生这类错误的第二个原因很有趣:一些开发人员倾向于从`ViewModel`开始显示进度条，他们需要上下文。这个问题的解决方案是使用密封类和活动数据。要了解更多信息，请阅读本文。

# 清除资源

作为应用程序开发人员，一项至关重要的任务是在不耗尽移动健康的情况下有效地管理资源。为了实现这一点，清理资源对于有效地使用它们同样重要。

在现代 Android 开发中证明这一点的最好例子之一是维护一个`CompositeDisposable`,并向其中添加所有与 RxJava 相关的任务。然后在`onCleared`函数中立即清除它们。这将减少内存泄漏并提高性能，因为我们清除了不必要的资源。

如果您正在使用协程，那么您可以用协程作用域做同样的事情。在适当的组件中维护一个范围是一个好的实践，比如`ViewModel`、片段和活动来限制功能。

# 带底座延伸

在上一节中，我们看到了当不再需要资源时清除它们的好处。虽然它很好，但也需要大量的手工操作。

减少手工工作的最好方法之一是维护一个带有开放关键字的基础组件(如`Baseviewmodel`)并执行常见任务。

这不是什么新鲜事。这是 Android 环境中的标准继承层次。Android 中的`Application`类是 Android 应用程序中的基类，包含所有其他组件，如活动和服务(例如 appcompatativity>FragmentActivity>Activity)。

我们可以对其他组件做同样的事情，比如片段和活动，以集中公共任务并减少重复代码。

# 不要实现广泛的业务逻辑

创建`ViewModels`是为了将与 UI 相关的代码与其他任务分开，但是`ViewModels`中大量的业务逻辑可能会复制样板代码。

我们需要将业务逻辑和其他广泛的操作从`ViewModels`转移到用例或存储库中来解决这个问题。这将为我们提供重用它们的杠杆，并使它们易于测试和维护。

# 奖金

要了解有关高级 Android 开发的更多信息，请阅读以下文章:

*   [“安卓产品口味”](https://medium.com/@sgkantamani/android-product-flavors-eb526e35f9f1)
*   [“如何创建和发布 Android 库”](https://medium.com/@sgkantamani/how-to-create-and-publish-an-android-library-f37bf715932)

目前就这些。希望你学到了有用的东西。感谢阅读！