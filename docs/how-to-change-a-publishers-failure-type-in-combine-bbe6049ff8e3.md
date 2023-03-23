# 如何在联合收割机中更改发布者的失败类型

> 原文：<https://betterprogramming.pub/how-to-change-a-publishers-failure-type-in-combine-bbe6049ff8e3>

## 当事情失败时，比错误更明确

![](img/1d2ab26fbaa25c9b7f5f19f73bf89820.png)

由 [Luca Bravo](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Combine 的特性之一是它的错误机制，使用起来有些痛苦。在 Combine 中，发布者有一个`Output`类型和一个`Failure`类型。`Output`表示发布者可以发出的值，而`Failure`表示发布者可以发出的错误。这真的很方便，因为您确切地知道从您订阅的发布者那里会得到什么。

但是当你有一个稍微复杂一点的设置时会发生什么呢？如果你想把一个发布者的输出转换成一个新的发布者，但是新旧发布者的错误不一致，会发生什么？

前几天，有人问我一个关于这个的问题。这个人想知道他们如何在`Publisher`上写一个扩展，将`URLRequest`值转换成`URLSession.DataTaskPublisher`值，这样每个发出的`URLRequest`就会自动变成一个网络请求。下面是我最初的实验(或者更确切地说，是我想写的代码):

不错吧？但是这个不编译。控制台中出现以下错误:

```
instance method ‘flatMap(maxPublishers::)’ requires the types ‘Self.Failure’ and ‘URLSession.DataTaskPublisher.Failure’ (aka ‘URLError’) be equivalent
```

简而言之，`flatMap`要求源发布者和我正在创建的发布者的错误是相同的。这有点麻烦，因为我不知道源发布者的错误到底是什么。我也不知道是否/如何将其映射到`URLError`或者是否可以将`URLError`映射到`Self.Failure`。

幸运的是，我们知道`Publisher.Failure`必须符合`Error`协议。这意味着我们可以完全删除错误类型，并用 Combine 的`mapError(_:)`操作符将其转换成通用的`Error`:

注意，我将`mapError(_:)`应用于`self`，即源发布者，以及在`flatMap`中创建的`URLSession.DataTaskPublisher`。这样，两个发布者都会发出一个通用的`Error`,而不是他们的特殊错误。好的一面是这段代码编译后。缺点是，当我们订阅在`performRequest`中创建的发布者时，我们需要找出可能发生了哪个错误。

彻底清除错误的另一种方法是将源发布者发出的任何错误映射到失败的`URLRequest`:

我更喜欢这个解决方案，因为我们不会丢失所有的错误信息。这里的缺点是我们不知道哪个错误可能发生在上游。这两种解决方案都不理想，但这里的重点不是让我告诉你哪种解决方案最适合你的应用程序。重点是，您可以看到如何使用`mapError(_:)`来转换出版商的价值，使其符合您的需求。

在结束这篇快速技巧之前，我想向您展示一个扩展，您可以使用它将任何发布者的输出转换成通用的`Error`:

您可以按如下方式使用此扩展:

虽然不多，但是节省了几行代码。不过，使用这个操作符时要小心。您会丢失来自上游发布者的所有错误细节，而倾向于稍微好一点的可组合性。

我个人认为，当您将错误转换为下游需要的错误时，您的代码会更加健壮，就像我在第二个示例中所做的那样。它确保您显式地处理任何错误，而不是忽略它们。