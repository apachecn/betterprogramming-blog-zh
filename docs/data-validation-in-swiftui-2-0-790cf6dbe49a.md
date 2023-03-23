# SwiftUI 2.0 中的数据验证

> 原文：<https://betterprogramming.pub/data-validation-in-swiftui-2-0-790cf6dbe49a>

## 看看你需要完全验证用户输入的一些数据的代码

![](img/b06e62ea40da5ff6bb915efecb10f6d0.png)

安德斯·吉尔登在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

许多最早的病毒利用依赖于很差的/没有数据验证。事实上，这个问题直到今天仍然存在，开发人员似乎忘记了数据验证。这是一个需要避免的大洞，这并不难做到。有时候做正确的事情是相当枯燥和费时的。

考虑到这一点，我想我会试着提供一个简单的例子，供您在需要时复制和粘贴。要求是输入一个 PIN 号，尽管它实际上可以是任何字符串。

这是初稿:

但是等等，不完整。它不会对您输入的字符数进行任何检查。为此，您只需在`onChange`块中再添加一个检查:

```
if name.count > 6 {
  name.removeLast()
}
```

这将把我们的输入限制在六位数，对我来说这似乎是一个合理的 PIN。当然，正如我已经提到的，您可以很容易地扩展/更改它来查看不同的集合。例如，如果您想要字母字符，请将其设为`isLetter`。

但在你走之前，我想我们还能做点什么。在此处添加修饰符，将键盘类型更改为数字小键盘，将对齐方式更改为居中，并将框架更改为更适合:

```
.keyboardType(.numberPad)
.multilineTextAlignment(.center)
.frame(width: 128, height: 32)
```

并将`TextField`更改为这样，以便在获得焦点时替换单词`PIN`:

只是多做了一些调整。通过给`VStack`本身添加一个提醒视图修饰符，添加一个提醒以确保信息传达出来，字母不是你所需要的:

```
.alert(isPresented: $showAlert) {
  Alert(title: Text("Important message"), message: Text("Numbers Only"), dismissButton: .default(Text("Ok")))
}
```

并且:

![](img/2a55ec0e4c374ef464a2abbeb5106d82.png)

简单 PIN 数据验证的动画 GIF

在`VStack`上加一个`onTapGesture`(你应该改成`ZStack`)，当你点击 PIN 窗口外的任何地方时，它会关闭键盘。这是你在上面看到的动画 GIF 的最终代码:

我这里没用`Color.clear`是因为没用。不透明度为零的`Color.yellow`也没有。我所做的看起来是最好的妥协。

我还在`onEditingChanged`开关的假臂上添加了一个`“go verify”`，当用户输入他们的 PIN 码时，它会做一些事情。

保持冷静，继续编码。