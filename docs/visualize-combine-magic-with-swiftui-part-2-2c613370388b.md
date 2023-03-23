# 可视化结合魔术和 SwiftUI 第 2 部分

> 原文：<https://betterprogramming.pub/visualize-combine-magic-with-swiftui-part-2-2c613370388b>

## 联合收割机中的运算符、订阅和取消

![](img/a703bcf1a82d39fed77e894d3def823f.png)

照片由 [Aziz Acharki](https://unsplash.com/@acharki95?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/magic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 在我们操场上进行联合作战

在本章的最后，我们将有一个主/细节格式的组合操作符列表(`[map](https://developer.apple.com/documentation/combine/publisher/3204718-map)`、`[filter](https://developer.apple.com/documentation/combine/publisher/3204709-filter)`、`[scan](https://developer.apple.com/documentation/combine/publisher/3229094-scan)`和`[dropFirst](https://developer.apple.com/documentation/combine/publisher/3204707-dropfirst)`)。您可以选择其中一个，并随时开始订阅和取消。

在我们深入研究更多组合操作符之前，我们需要稍微修改一下我们的操场。在前面的[章节](https://medium.com/flawless-app-stories/visualize-combine-magic-with-swiftui-part-1-3a56e2a461b3)中，我们一次只显示一个流视图。现在，我们想在现有视图的正下方添加另一个流视图，以便并排比较。

下面是我们为呈现`map`操作符而创建的新视图。

CombineMapStreamView

## 添加值状态

首先，我们添加一个额外的流，让它们一起工作。

```
@State **private** **var** stream1Values: [String] = []@State **private** **var** stream2Values: [String] = []
```

流 1 和流 2 是在 SwiftUI 视图上发出值的两个流的真实来源。

## 添加一次性套件

```
@State **private** **var** disposables = Set<AnyCancellable>()
```

还记得`AnyCancellable`吗？既然我们已经订阅了两个流，我们需要在内存中保存两个`AnyCancellable`实例。

因为我们计划让两个流同时接收和完成(或取消订阅)，所以我们将它们保存在一个集合中(有点像 [RxSwift 的 DisposeBag](https://github.com/ReactiveX/RxSwift/blob/master/Documentation/GettingStarted.md#dispose-bags) ) *。* `AnyCancellable`有这个方便的扩展方法[存储](https://developer.apple.com/documentation/combine/anycancellable/3333294-store)，可以保持代码的整洁。

```
publisher.sink {
  **self**.stream1Values.append($0)
}.store(in: &**self**.disposables)
```

如您所见，`AnyCancellable`存储在一次性用品集中。

## 添加发布者

现在，让我们来看看出版商。

```
**let** publisher = **self**.invervalValuePublisher()
```

方法`intervalValuePublisher`每秒钟从 1 到 5 依次发出数值。如果你没有读过第 1 部分，你可以在那里找到更多的细节。

## 运算符示例:地图

```
**let** mapPublisher = publisher.map { (Int($0) ?? 0) * 2 }.map { String($0) }.eraseToAnyPublisher()
```

`MapPublisher`是这个例子中的主角。它将发布者发出的每个值乘以 2。

## 操作员示例:扫描

简单吧？现在我可以很容易地用这个*组合扫描视图*演示另一个操作符[扫描*、*T21。](https://developer.apple.com/documentation/combine/anypublisher/3229063-scan)

```
**let** scanPublisher = publisher.map { Int($0) ?? 0 }.**scan(0) { $0 + $1 }**.map { String($0) }
```

您在这里看到的前两个映射只是字符串和积分器之间的类型转换。我们使用 scan 来观察流中的每个值，并将它们加到下一个值上(类似于 Swift Array 的 [reduce](https://developer.apple.com/documentation/swift/array/2298686-reduce) 方法)

如您所见，我们现在可以应用相同的规则为所有其他操作符创建视图:`filter`、`drop`、`merge`、`FlatMap`等。

# 组织联合运算符

既然我们知道了如何可视化组合操作符，我们希望能够对它们进行一点组织。

理想情况下，应该有一个可供选择的操作符列表，我们应该能够使用 SwiftUI 的 awesome NavigationView 进行导航，并深入查看每个操作符。然而，我不想为每个操作者创建新的视图。让我们重用一个`CombineStreamView`，并为第二个流传递一个带有我们想要的任何操作符的 publisher。

## 使操作员视图通用

让我创建另一个名为`GenericCombineStreamView`的视图。

```
var comparingPublisher: (AnyPublisher<String, Never>) -> (AnyPublisher<String, Never>)
```

正如您所看到的，这个视图采用了一个负责修改发布者(带有操作符)的闭包。

```
**let** comparingPublisher = **self**.comparingPublisher(publisher)
comparingPublisher.sink {
    **self**.stream2Values.append($0)
}.store(in: &**self**.disposables)
```

我们通过将原始发布者传递给闭包来获得被操作的发布者。然后像以前一样订阅。

# 放在 SwiftUI 列表中

我们把它们放在一起，再加几个运算符。

```
**func** filterPublisher(publisher: AnyPublisher<String, Never>) -> AnyPublisher<String, Never> {
  publisher.filter { $0 != "2" }.eraseToAnyPublisher()
}**func** dropPublisher(publisher: AnyPublisher<String, Never>) -> AnyPublisher<String, Never> {
  publisher.dropFirst(2).eraseToAnyPublisher()
}
```

除了现有的`map`和`scan`操作符，我们还添加了`filter`和`drop`T6 的例子。

现在我们在一个地方有了所有例子的列表。

[下面是源代码](https://github.com/kevinjohnason/combine-magic-swiftui.git)。

# 下一章

我们已经成功地创建了一个操场，它可以扩展到足以呈现单个操作流的列表。但是多操作流(例如，`map`和`filter`)呢？或者来自多个流的单操作流如何(例如，合并两个流)。

在下一章的[中，我们将再次扩大我们的游乐场，以容纳那些经营者。](https://medium.com/@kevinminority/visualize-combine-magic-with-swiftui-part-3-a3f0cc42bcc8)