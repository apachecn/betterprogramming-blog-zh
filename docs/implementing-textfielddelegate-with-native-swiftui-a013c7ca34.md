# 使用本机 SwiftUI 实现 TextFieldDelegate

> 原文：<https://betterprogramming.pub/implementing-textfielddelegate-with-native-swiftui-a013c7ca34>

## 使用本机 SwiftUI 了解 TextFieldDelegate 方法

![](img/cad84c66a633a7c8e58aa447aab00849.png)

照片由 [Dries Augustyns](https://unsplash.com/@driaug?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

现在，离 iOS 15 的生产发布还有不到一周的时间，我仍然听说 SwiftUI 还没有准备好生产。可悲的是，它正在成为一个城市神话。

这是真的还是当权者不够努力？说实话，有一定道理。`TextView`和`ScrollView`都是 UIKit 中 UI 元素的很好的例子，它们在 premier 情况下不存在于 SwiftUI 中，或者后者只被部分实现。但这是一个非常动态的目标，只要付出一点努力，你就能获得非常好的结果。

鉴于 SwiftUI 的布局能力领先 UIKit 好几光年，我认为这是值得的。

让我们看另一个例子，`TextField`。这已经在 SwiftUI 中实现了，但是从表面上看，like `ScrollView`并没有完全实现。请和我一起踏上旅程，用更多的精力来研究这个问题。我想尝试用所有的修饰来实现`TextField`，而不是诉诸`UIViewRepresentable`。我想要一个本地解决方案。

# 案情摘要

UIKit 中的`TextField`带有一个代理来帮助你使用它。在委托协议中，我发现有六个回调函数用于管理编辑，三个用于编辑文本，还有一个用于报告文本选择的变化。

现在我想尽可能多地实现这些功能，而不求助于`UIViewRepresentable`。

我们应该从实现一个扩展来报告键盘是否出现在屏幕上开始。

如果存在，那么显然有人点击了你的`TextField`。当然，如果我们有多个 TextField，我们可能不知道哪个是 SwiftUI。这是一个苹果已经[在 iOS 15 中解决了](https://developer.apple.com/documentation/swiftui/menu/focused(_:))的疏忽，你会很高兴地知道，但我不打算在这里覆盖它。尽管我认为可以围绕它编写一些代码，但请继续阅读。让我们从检测键盘的代码开始。

好吧——当你在使用它的时候，这里是收起键盘的代码——你也需要它。

```
func hideKeyboard() {
UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
}
```

当然，我们有带`onCommit`和`onEditingChanged`标签的 SwiftUI。它们之间的这两个标签覆盖了`textFieldDidDidBeginEditing`和`textFieldDidDidEndEditing`州。

要管理文本本身的编辑，您可以使用`onChange`标签，它可以报告对`TextField`的更改并做出相应的响应。

在代码示例中，我在这里展示了我们将输入限制为仅大写字母:

快速回顾一下代码的其余部分:

*   我们使用一个类来包含文本输入框中引用的变量，这样我就可以轻松地在 SwiftUI 代码之外访问它。
*   在那里使用了`willSet`和`didSet`模板——我可以添加更多的`PassThroughSubjects`。
*   两个神奇的数字`123`和`789`只不过是对字段的引用，所以我可以判断哪个是当前活动的。
*   这两个字段在出现时都是启用的，显然，如果这样做更有意义，您可以用不同的方式编写代码。
*   使用模板 SwiftUI 输入文本`isEditing`标志和`currentFieldPublisher` ID，控制发送哪个(开始编辑或结束编辑)消息。
*   当您点按字段时显示键盘，当您按下 return 键时关闭键盘。
*   增加了一些`PassThroughSubjects`来响应开始/结束+清除+启用/禁用组合消息。

这里的动画 GIF 显示了不同的消息和字段引用。当您在框中单击数字时，会出现后者。开始编辑显示为绿色圆圈，结束编辑显示为红色圆圈…橙色表示两者都不是。很明显，当我没有在第一个单词 bat 上输入大写字母时，你什么也看不到。

![](img/eb40346fe6f5c4cb71adc0ec18ee30ec.png)

SwiftUI TextField 的实际实现

# 结论

代码中还有几个标签，没什么特别的——只是标准票价。所有这些让我想到这篇短文的结尾。霍普也认为这并不像有些人想让我们相信的那样糟糕。保持冷静，继续编码。