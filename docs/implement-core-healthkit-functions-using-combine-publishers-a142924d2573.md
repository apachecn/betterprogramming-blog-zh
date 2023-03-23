# 使用联合发行商实现核心 HealthKit 功能

> 原文：<https://betterprogramming.pub/implement-core-healthkit-functions-using-combine-publishers-a142924d2573>

## 让我们为 HealthKit 创建发布者

![](img/04c3f082c72f3cb16b4335ad46bb3a2b.png)

林赛·亨伍德在 [Unsplash](https://unsplash.com/) 上的照片。

[Combine](https://developer.apple.com/documentation/combine) 是一个用于编写异步代码的强大框架。它提供了像发布者和操作者这样的构件，允许程序员编写复杂的数据管道、网络调用等等。 [HealthKit](https://developer.apple.com/documentation/healthkit) 是苹果提供的另一个框架，它管理健康相关数据的读写。iOS 和 watchOS 上都有。

在本文中，我将探讨如何使用 Combine 来实现一些 HealthKit 基本功能——即请求访问以读取用户数据并查询该数据。

# **请求授权**

请求授权是一个重要的步骤，我们需要在查询任何健康数据之前进行。用户可以随时通过健康应用或设置更改权限，而我们的应用不会收到这些更改的通知。

为了完成这个任务，我们将一组`HKSampleType`传递给实例方法`requestAuthorization(toShare:read:completion:)`。我们可以指定我们想读或写的不同类型。

受[最新苹果女性健康研究更新](https://www.apple.com/newsroom/2021/03/apple-womens-health-study-releases-preliminary-data-to-help-destigmatize-menstrual-symptoms/)的启发，我们将在 HealthKit 中查询症状数据。特别是腹部绞痛(研究参与者最常记录的症状)。您可以查看可用的[症状类型列表](https://developer.apple.com/documentation/healthkit/data_types/symptom_type_identifiers)(有一大堆)。下面是请求授权方法的一个简单实现:

这是一种很好的简单的请求访问的方法，但是让我们看看 Combine 如何通过一个`Future`帮助我们做得更好。

# **请求期货授权**

根据[官方文档](https://developer.apple.com/documentation/combine/future)，a `Future`是“最终产生单一值，然后完成或失败*的发布者。”*它非常适合包装一次性授权请求。因为它是一个发布者，我们将能够使用 Combine 操作符来应用我们想要的任何转换。

`Futures`也非常适合将命令式编写的代码转换成声明式代码。要初始化一个，我们必须向它提供一个承诺。在 Combine 中，承诺只是需要一个`Result`的普通闭包。以下示例使用`Future`请求授权:

让我们来看看`authorizationPublisher()`方法的签名。它返回一个`Future<Bool, Error>`。注意，它不需要任何完成处理程序。在函数内部，我们用闭包初始化了一个`Future`。在这个闭包中，我们将执行请求授权的异步任务。不同之处在于，我们称之为履行承诺，而不是称之为完成。如果出现任何错误，我们调用结果为`.failure(error!)`的承诺。如果没有，我们调用 promise，并将`authSuccess`布尔传递给它。

如果您运行这段代码，您会注意到`Future`一创建就执行了。这就是他们的工作方式，不像其他联合出版商。在某些场景中，我们可能希望随时初始化我们的发布者，并且只在订阅时触发授权流。我们可以通过将`Future`包装在`Deferred`发布器中来实现这一点:

现在，`Future`闭包的执行将被推迟，直到它接收到一个订阅者，允许我们调用`authorizationPublisher()`方法并传递我们的发布者，而无需触发 HealthKit 授权流。`Deferred`还将保证对同一个 publisher 实例的每个新订阅将导致闭包再次执行。

# **检查保健包是否可用**

到目前为止，这个授权请求实现还没有考虑受支持的设备系列。我前面提到过，HealthKit 只在 iOS 和 watchOS 上可用。要检查当前设备是否受 HealthKit 支持，我们只需调用`isHealthDataAvailable()`。如果不是，我们将使用自定义`HealthDataError`枚举的`unavailableOnDevice`用例:

需要注意的是，当`authSuccess`为`true`时，并不一定意味着用户已经被授权访问。这意味着我们成功地请求了它。为了保护用户隐私，如果用户拒绝了请求，HealthKit 不会通知我们。

# **查询症状数据**

相同的 Combine-ish 方法可以应用于编写 HealthKit 查询。这正是 Combine 大放异彩的时候，它允许我们将 HealthKit 样本类型转换为我们的应用程序所使用的模型。如果你想深入了解如何实现查询的细节，苹果公司有一本[很棒的指南](https://developer.apple.com/documentation/healthkit/reading_data_from_healthkit)。TL；DR 版本是我们将使用一种最简单的访问健康数据的方式`HKSampleQuery`。

我们将使用一个`PassthroughSubject`，一个内置的发布器，它为我们提供了一种通过调用`.send(_:)`将值注入到流中的方法:

一个`PassthroughSubject`将不存储任何值。它只会将它们发送给订阅者(不像`CurrentValueSubject`，它会存储最近发布的值)。

`Subjects`是另一个将命令式代码集成到声明式 API 的工具。我们还可以为 HealthKit 查询定义一个定制的发布者，但是这需要更多的样板文件来完成大致相同的行为。`Subjects`和`Futures`在大多数情况下会起作用，但是如果需要更多的灵活性，那么定制发行商可能值得一试。

现在，我们可以将两个发布者结合起来，构建一个数据管道，异步请求授权，查询 HealthKit，并将查询到的示例映射到我们的定制模型类型:

`.flatMap`操作符将使用查询主题替换发出的认证布尔值，并将它们展平，这样我们就不会以嵌套发布者结束。

*免责声明:我使用* `*.assertNoFailure()*` *表示这个发布者永远不会失败，因此将其错误类型改为* `*Never*` *。在实际场景中，我们应该在断言没有失败之前捕捉错误并正确处理它们。*

# **批量查询健康工具包**

我之前提到的女性健康研究更新报道了一系列其他症状，如腹胀、疲劳、痤疮和头痛。让我们将它们添加到我们的示例中:

HealthKit 中没有“批处理查询”类型，但是我们可以使用我们之前编写的扩展和一些组合操作符来实现该行为:

这里有一些新的代码:

1.  迭代`symptomTypes`数组，为每个症状创建一个`subject`。
2.  `CompactMap`样品到`Symptom`型号。

结果类型是一个发布者数组:`[AnyPublisher<[Symptom], Error>]`。不完全是我们想要的。与一系列发行商合作意味着订阅每一个发行商，处理多个订阅，等等。幸运的是，Combine 拥有合并出版商的强大工具。我们来看看其中的几个:`Publisher.Merge`和`reduce`。

`Merge`是一个发行商，它可以将一个、两个、三个或*多个*发行商合并成一个。我们将使用后面的变体`MergeMany`，并扩展`Sequence`协议，将它的元素合并到一个发布者中:

有了这些，我们将不再需要与一系列出版商打交道。然而,“批量查询”的幻想破灭了，因为这个发布者将在每个查询返回时发出值。这就是`reduce`操作符发挥作用的地方。它的工作方式类似于`Sequence`reduce——我们提供一个初始值和一个闭包来组合后续值。由于我们的目标是“一批”症状，初始值将是一个空数组，我们将把每个发出的数组加在一起:

几行代码中包含了很多内容，所以让我们一步一步来看:

1.  再次重复`symptomTypes`并为每个症状创建一个主题。这一步的结果类型是一个发布者数组。
2.  将发布服务器的数组合并成一个发布服务器。
3.  使用`reduce`收集流的元素，并发布一个包含所有元素的数组。
4.  清除此发行者的类型以获得更清晰的签名。

就是这样！发布者将发出一个包含所有被查询症状的数组。对于这个例子，我已经简化了管道，所有的样本都被转换成一个单一的`Symptom`类型。但是，您可以使用相同的方法来组合多个查询，并将它们映射到不同的模型类型。

# **何去何从**

概括地说，我们使用`Futures`来编写授权请求流。然后，使用`subjects`，我们为 HealthKit 示例创建了一个发布者。我们看到了如何用`MergeMany`组合多个发布者，以及如何用像`flatMap`和`reduce`这样的操作符扩展它们的行为。

这些简单但富有表现力的代码可以完成日常任务。请求授权和异步读取和转换数据对于许多类型的应用程序来说都是常见的，无论它们是否使用 HealthKit。试着找出可以从声明性中获益的命令式 API，尝试一下，然后告诉我！

感谢您的阅读。

# **资源**

1.  [女性健康研究更新](https://www.apple.com/newsroom/2021/03/apple-womens-health-study-releases-preliminary-data-to-help-destigmatize-menstrual-symptoms/)
2.  [请求授权文件](https://developer.apple.com/documentation/healthkit/hkhealthstore/1614152-requestauthorization)
3.  [唐尼·沃尔斯的《伟大的未来》一文](https://www.donnywals.com/using-promises-and-futures-in-combine/)
4.  [苹果从 HealthKit 读取数据的指南](https://developer.apple.com/documentation/healthkit/reading_data_from_healthkit)
5.  [health kit 入门— WWDC20](https://developer.apple.com/videos/play/wwdc2020/10664/?time=400)