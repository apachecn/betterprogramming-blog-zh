# 将闭包作为目标添加到 Swift 中的 UIButton 和其他控件

> 原文：<https://betterprogramming.pub/adding-a-closure-as-a-target-to-uibutton-and-other-controls-in-swift-2c5e66029817>

## 这个经常被请求的 API 终于到来了

![](img/57b9a6ca3984eb212e687674c2e6fb6f.png)

照片由 [engin akyurt](https://unsplash.com/@enginakyurt?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

目标动作模式与用户界面控件结合使用，作为对用户事件的回调。每当在目标上按下一个按钮，它的动作就会被调用。事实上，该方法的定义并不接近控件定义，这有时被视为一个缺点，也是许多美国开发人员在 Stack Overflow 等网站上搜索基于闭包的解决方案的原因。

iOS 14 SDK 引入了新的 API，允许我们将 UIControls 与闭包结合使用。常见的`UIControl`元素有`UIButton`、`UISegmentedControl`和`UISwitch`，但是还有很多都是从`UIControl`对象继承而来的。所有这些界面元素现在都可以与这个新的 API 一起使用。

# 使用带有闭包回调的 UIControl

您很可能熟悉下面这段代码:

每次点击按钮时都会调用`buttonTapped(_:)`方法。

同样的代码现在可以写成如下形式:

这使操作接近控件定义，从而提高代码的可发现性。

## 获取对控件发送方的引用

上面这段代码的一个不同之处是我们的方法引用了发送者。在某些情况下，当您想知道哪个控件调用了链接方法时，这可能会很方便。

有了新的基于闭包的 API，您可以使用`action`参数来访问发送者。例如，当您想要从文本字段中读出文本时:

```
let textField = UITextField()
textField.addAction(UIAction(title: "", handler: { action in
    let textField = action.sender as! UITextField
    print("Text is \(textField.text)")
}), for: .editingChanged)
```

# 我应该总是使用新的闭包 API 吗？

现在到处使用闭包可能很有诱惑力。但是，控制动作很容易在代码中增长，从而降低代码的可读性。在这些情况下，您可能希望回到旧的目标-动作模式，这种模式允许您使用单独的方法。

当您的控制操作需要多行代码时，Swift 中的方法更容易阅读。

# 结论

iOS 14 SDK 中新的基于闭包的 API 是一个受欢迎的变化，但应该谨慎使用。使用许多闭包很容易使你的代码可读性更差，只有在控件回调逻辑可以用几行代码编写的情况下才应该使用。否则，最好使用老式的目标-行动模式。

感谢阅读！