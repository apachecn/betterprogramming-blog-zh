# 2021 年开始使用联合框架

> 原文：<https://betterprogramming.pub/getting-started-with-the-combine-framework-in-2021-e8c1bfb9db1a>

## 在生产代码中使用 Combine 需要知道的一切

![](img/c821ea884a72198f8ff3c596aa7963b3.png)

约书亚·雷德科普在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

苹果在 2019 年推出与 iOS 13 结合的框架已经有一段时间了，但我最近才决定学习它，因为现在大多数活跃的 iOS 设备都运行 iOS 13。互联网上有大量的资源(免费或付费，阅读或代码),讨论各个级别的联合收割机。然而，我花了许多个晚上在浏览了无数篇文章后感到困惑，然后才感到有足够的信心在我当前项目的生产代码中编写我的第一个`import Combine`。

如果你在花时间学习了联合收割机之后有同样的感觉，这篇文章是为你准备的。

*本文仅介绍联合收割机的基础知识。例如，创建自定义*`*Publishers*`*`*Subscribers*`*等复杂用法将在单独的文章中介绍。**

# *联合收割机的主要参与者*

*在成为专家必须理解的所有新概念中，联合收割机有三个关键角色:`Publishers`、`Subscribers`和操作员。*

# *出版商*

*一个`Publisher`是一个带有签名`Publisher<Output, Failure>`的协议，并且期望传递零个、一个或多个相同类型的值。它必须指出它是否能发出错误。如果它发出错误，我们将它的失败标记为错误类型。如果它没有发出任何错误，我们使用`Never`作为失败。`Publisher`完成，无错误或出现`Failure`中指定类型的错误。*

*Combine 中的`Publisher`类似于新闻业中的出版商。期刊出版商(或出版社)提供内容，而不考虑格式。例如，它可以是出版周刊的传统出版社(例如*时代*杂志)。它输出多个值，并且不期望打破这种模式，这意味着你总是可以期待《时代》杂志每周都会出一期新的。*

*出版商的另一个例子可以是运营新闻出版服务的数字出版公司。你不知道你的手机会收到多少通知——一天一个或几个。他们可以不小心把 Lorem Ipsums 推送到你的手机上。或者当你点击通知时，它在打开应用程序时崩溃，而不是引导你到正确的文章。这就是发布者发出错误的方式。这与 Combine 的`Publisher`的区别在于，在 Combine 中，一旦`Publisher`发出一个错误，`Publisher`就被认为是完整的。就你的新闻应用而言，如果错误没有被修复，你会不断收到错误通知。*

*综上，一个`Publisher`:*

*   *有一个`Publisher<Output, Failure>`的签名。*
*   *随时间推移输出零个、一个或多个相同类型的值。*
*   *可以发布一个错误类型来通知`Publisher`已经失败，并且`Publisher`将完成。*
*   *可以没有任何错误地完成。*

## *有用的出版商*

*Combine 有一百万种方法来初始化一个`Publisher`。有专门的发布器供我们用于某些常见任务。*

## *就*

*[提供单个值](https://developer.apple.com/documentation/combine/just)并完成。*

*它不能因为一个错误而失败。这就是为什么输出值是初始化`Just`发布器所需的唯一参数。*

## *未来*

*[用闭包](https://developer.apple.com/documentation/combine/future)初始化，最终解析为单个输出值或失败完成。*

*`Future`就像 Javascript 中的承诺。它需要一个回调函数，在回调函数中，当你需要的时候，你可以自由地返回一个`.success`或`.failure`(例如，当你想做一个异步任务的时候)。处理完您的请求后，`Publisher`将发出您指定的值(即成功或失败)。*

## *@已发布*

*[一个属性包装器](https://developer.apple.com/documentation/combine/published)，为这个类型创建一个`Publisher`。然后可以使用`$`操作器访问`Publisher`。*

*使用`@Published`时要记住的一点是:`@Published`属性是受类约束的。只能为类的属性标记它，而不能为结构的属性标记它。如果你试图这样做，编译器会抱怨。它也不能在协议中使用。*

*虽然有其他方法可以达到同样的目的，`@Published`仅仅是让联合收割机来做繁重的工作。替代方案将在后面介绍。*

**为了学习更多关于属性包装器的知识，* [*约翰·桑德尔*](https://www.swiftbysundell.com/articles/property-wrappers-in-swift/) *写了一篇关于这个主题的广泛介绍*。*

## *出版商和基金会*

*苹果还为 Foundation 引入了许多助手，所以你不必每次想与 Foundation 交互时都要编写自定义的`Publisher`。*

# *订阅者*

*使用联合收割机的第一步是衍生一个`Publisher`。一个`Publisher`单独不能实现任何有用的东西，直到它被一个`Subscriber`附加。`Publishers`和`Subscribers`是要连接的。一个`Subscriber`是一个带有签名`Subscriber<Input, Failure>`的协议。将`Subscriber`连接到`Publisher`会产生一个`Subscription`。*

*假设你是*时代*杂志的订户(即你与*时代*杂志出版商有订阅关系)。这种订阅的一个特点是，当最新一期《时代》杂志出版时，你会收到一份。你期望收到一份*时间*——*而不是*国家地理*的副本。在 Combine 中确实如此:发布者的输出必须与订阅者的输入相匹配。发布服务器的失败类型必须与订阅服务器的失败类型相匹配。**

**你应该熟悉现今大多数订阅服务的取消政策。在订阅期内，您可以随时退出或取消订阅。或者，如果 *Time* 杂志停止营业，订阅将自动取消。你不会从杂志上得到任何新的问题。**

**Combine 允许您随时进行`cancel`订阅。**

**在一个`Subscriber`订阅了一个`Publisher`之后，你得到了一个符合`Cancellable`协议的对象:**

1.  **您可以通过拨打`subscription.cancel()`手动取消订阅。**
2.  **如果已经取消分配，订阅将自动取消。**

**存储订阅，因为如果取消分配，订阅将自动取消。**

**综上所述，`Subscriber`:**

*   **与一个订阅链接。一个`Subscriber`不能订阅多个`Publishers`。**
*   **`Subscriber`的输入和失败类型必须与发布者的匹配。**
*   **您需要将订阅存储在某个地方，以避免它被释放。**

## **有用的订户**

## **。接收器(接收完成:接收值:)**

**Combine 提供的一个方便的`Subscriber`T21，允许您处理`Publisher`发出的值。立即从`Publisher`请求一个值，然后等待输入值。**

**只要订阅保持有效，它就接受由`Publisher`发出的值，并将它们转发给`sink`。**

## **。分配(给:在:)**

**[将来自发布者的每个元素](https://developer.apple.com/documentation/combine/just/assign(to:on:))分配给对象的一个属性。**

***注意:在写的时候，使用* `*.assign(to: on: self)*` *会造成保留循环。这被报告为一个错误。有一个* [*临时变通办法*](https://forums.swift.org/t/does-assign-to-produce-memory-leaks/29546/9) *可用。***

## **学科**

**`Subjects`很特别，因为它们的行为类似于`Publishers`和`Subscribers`。通常情况下，`Subscriber`从`Publishers`获取输入。`Subjects`是`Publishers`，在它们被初始化之后，你可以自由地按需发送值。**

**Combine 为我们提供了两个方便而有用的主题。**

**[通过主题<输出，失败>](https://developer.apple.com/documentation/combine/passthroughsubject) :**

**`PassthroughSubject`没有初始值，也不在订阅期间的任何时间点存储任何值。它充当发布者，唯一的工作是将值传递给订阅操作。**

**[电流值 Subject <输出，故障>:](https://developer.apple.com/documentation/combine/currentvaluesubject)**

**`CurretValueSubject`的初始化需要初始值。您可以随时访问用`subject.value`存储的值。更改该值将导致主题向附加到它的订阅发出该值。**

## **PassthroughSubject 与 CurrentValueSubject**

**总之，比较`CurrentValueSubject`和`PassthroughSubject`:**

*   **`CurrentValueSubject`有初始值，而`PassthroughSubject`没有。**
*   **`CurrentValueSubject`在`.value`中存储一个值。改变该值将自动使`Publisher`发送一个新值。`PassthroughSubject`本身不存储任何值。它只是将上游值传递给下游。**

**`CurrentValueSubject`对于有状态的、可变的属性很有用，而`PassthroughSubject`对于表示事件很有用。**

# **经营者**

**Combine 的函数式反应范式在可读性和装饰性方面优于其他范式。所有的权力都在运营商手里。**

**运算符是定义在`Publisher`实例上的函数:**

*   **操作符被链接以添加处理步骤。**
*   **每个操作符返回一个新的`Publisher`。**

**运算符最好通过例子来解释。**

## **。map(_ transform: (Output) -> T)**

**Combine 的`[.map(_:)](https://developer.apple.com/documentation/combine/publisher/map(_:)-99evh)` [操作符](https://developer.apple.com/documentation/combine/publisher/map(_:)-99evh)的工作方式类似于您将在集合上使用的标准`.map(:)`函数。**

**`.map(_:)`使用闭包来转换它从上游接收的每个元素`Publisher`。**

**我们从一个整数数组开始，使用 Combine 的一个方便的方法创建了一个`Publisher`,将基础实体转化为发布者。当我们需要额外处理这些值时,`.map`就派上了用场。在这种情况下，上游值从整数转换为字符串。**

**运算符可以被链接。链接多个`.map()`让我想起了*单一责任原则*，在这里你可以为每个操作者做一件事。它极大地提高了合并代码的可读性。**

## **。filter(_ is included:(Output)-> Bool)**

**`[.filter()](https://developer.apple.com/documentation/combine/just/filter(_:))`工作原理类似。它接受一个闭包，只发出满足闭包中指定要求的值。其他值不会向下游发出。**

**操作员应该一起工作。在上面的例子中，我们首先过滤掉奇数，然后将它们转换成字符串。最后，我们可以使用值。**

## **。删除重复项()**

**[从上游`Publishers`移除重复元素](https://developer.apple.com/documentation/combine/fail/removeduplicates())。**

**`.removeDuplicates()`不记录发出的所有值，但使用双元素存储器。该比较基于当前值和先前值。**

**例如，如果值为`0, 1, 1`，则发出的值将为`0`和`1`。如果值是`0, 1, 0, 1`，发出的值仍然是`0, 1, 0, 1`，因为没有相邻的元素相等。**

## **。compact map(_ transform:(Output)-> T？)**

**`[.compactMap()](https://developer.apple.com/documentation/combine/publishers/catch/compactmap(_:)/)`接受一个返回可选的转换闭包，只有非零可选结果将被返回到下游`Publisher`。无选项将被删除。**

## **。组合测试

(_ 其他:P)** 

**在一个`Publisher` [上调用该方法来组合多个](https://developer.apple.com/documentation/combine/just/combinelatest(_:)) `[Publishers](https://developer.apple.com/documentation/combine/just/combinelatest(_:))`。当 any 发出一个值时，下游的`Subscriber`将从组合的`Publishers`中接收一个最新元素的元组。**

**在组合的`Publisher`产生任何值之前，每个发布者必须至少产生一个值。**

**`.combineLatest()`支持链接。你可以把 unlimited `Publishers`和这个语句结合起来。如果您需要组合少于四个`Publishers`，combine 在`Publishers`上为您的命令提供了方便的静态方法:**

*   **`Publishers.CombineLatest(a: _, b: _)`**
*   **`Publishers.CombineLatest3(a: _, b: _, c: _)`**
*   **`Publishers.CombineLatest4(a: _, b: _, c: _, d: _)`**

**感谢阅读。**