# Swift 中异步组合代码的单元测试

> 原文：<https://betterprogramming.pub/unit-testing-asynchronous-combine-code-using-xctest-framework-3066f570f200>

## 让我们使用 iOS 中的 XCTest 框架来测试一个组合代码库

![](img/2a3d4580616dc628530596bb83d407aa.png)

普通技术人员在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Combine 是苹果在 iOS 13 中发布的一个反应式编程框架，允许我们处理异步数据流。

[根据苹果](https://developer.apple.com/documentation/combine)的说法，联合收割机可以让我们:

> 通过组合事件处理运算符来自定义异步事件的处理。

在本文中，我们将探讨如何使用 XCTest Apple 的框架对基于 Combine 的代码进行单元测试。

为了做到这一点，我们将为一个示例应用程序编写单元测试，该程序将显示一个作业列表。我们将使用的核心类是:

`Job`:表示作业实体。我们的模型。

`JobsViewModel`:负责处理所有的展现逻辑。我们将使用两种视图状态:`Populated`和`Empty`。

`JobClientProtocol`协议将作为我们网络逻辑的门面。

在您的应用程序中，您应该有一个符合它的`JobClient`类型，但我们不会在本文中实现它(特别是因为我们不需要它):

**注意**:为了简单起见，`JobsViewModel`将`JobClientProtocol`作为依赖项(理想情况下，我们希望有一个额外的层，这样我们的视图模型就不会直接与网络代码交互)。

# 将测试什么

我们的主要目标是测试视图模型中的所有视图状态是否根据提取的作业正确设置。

我们将测试的两种状态及其条件是:

1.  `Populated`:如果我们有一个或多个提取的作业。
2.  `Empty`:如果没有要显示的提取作业。

# **创建模拟**

我们先来嘲讽一下我们的`JobClientProtocol`。这应该不是一个困难的任务，因为我们的视图模型依赖于抽象(在这种情况下是一个协议)而不是具体的类型。所以一个`MockJobClient`应该是这样的:

这将允许我们按照自己的意愿控制网络客户端的行为，为我们编写单元测试提供了很大的灵活性。

**注意**:我们使用`AnyPublisher`是因为这种形式的类型擦除保留了抽象，因此隐藏了我们网络客户端的底层实现。

# 定义我们存储的属性

首先，我们需要声明两个存储属性:

1.  `viewModelToTest`:这将是将要测试的视图模型的实例。也称为 SUT(被测系统)。
2.  `mockJobClient`:我们被嘲笑的一个实例`JobClientProtocol`。

我们需要为其中的每一个存储属性，因为我们将直接修改`mockJobClient` 来模拟我们需要的不同场景(`MockJobClient` *是*类*，*因此它将作为视图模型初始化器中的引用被传递，并且在`MockJobClient`实例上执行的任何更新或修改都将影响视图模型行为)*。*

最后，我们将有一个`AnyCancellable` 集合*，*的实例，因为我们将一个订阅者附加到由`mockJobClient`返回的发布者，并且这个订阅者需要被保留；否则，它将立即被释放:

```
**private** **var** cancellables: Set<AnyCancellable> = []
```

# 编写我们的单元测试

为了执行我们的单元测试，我们需要订阅`JobsViewModel`中的`viewState`发布者。为此，我们将使用`sink`方法，该方法将使用基于闭包的行为将订户附加到`viewState` :

```
viewModelToTest.$viewState.dropFirst().sink { state **in** // Evaluate state here}.store(**in**: &cancellables)
```

下一步是模拟网络客户端响应。通过使用我们的`MockJobClient`，我们可以很容易地做到这一点。要模拟空响应，我们应该这样做:

```
mockJobClient.fetchJobsResult = Result.success([]).publisher.eraseToAnyPublisher()
```

为了模拟群体反应:

```
**let** jobsToTest = [Job(id: "1", title: "title", description: "desc")]mockJobClient.fetchJobsResult = Result.success(jobsToTest).publisher.eraseToAnyPublisher()
```

最后一步是调用`loadJobs`方法，该方法将使用`MockJobClient` *:* 启动检索过程

```
viewModelToTest.loadJobs()
```

**注意**:重要的是在前面的步骤(附加订户和模拟联网响应)之后调用`loadJobs`方法；否则，我们的`viewState`发布者将不会发出任何值，因为在这一点上，还没有订阅者被附加(如果没有订阅者被附加，那么 Combine 的发布者不会发出任何值)。

为了验证我们正在接收正确的状态，我们将使用`XCTestExpectation`，这是 Apple 为我们提供的一个类，用于创建异步测试。

最终的测试类如下所示:

仅此而已。您可以在这个资源库中找到完整的示例代码:

[](https://github.com/DeluxeAlonso/CombinePlayground/blob/development/Combine.playground/Pages/Unit%20testing.xcplaygroundpage/Contents.swift) [## combine playground/contents . swift at development DeluxeAlonso/combine playground

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/DeluxeAlonso/CombinePlayground/blob/development/Combine.playground/Pages/Unit%20testing.xcplaygroundpage/Contents.swift)