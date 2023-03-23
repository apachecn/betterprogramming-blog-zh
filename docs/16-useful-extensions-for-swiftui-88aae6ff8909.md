# SwiftUI 的 16 个有用扩展

> 原文：<https://betterprogramming.pub/16-useful-extensions-for-swiftui-88aae6ff8909>

## 我在项目中反复使用的一些有用的代码片段

![](img/8412932cde65eae2b4fb73174b02cdb3.png)

由 [KS KYUNG](https://unsplash.com/@mygallery?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

随着我们越来越接近 WWDC 2022，可能还有 SwiftUI 的 4.0 版本，这是一个我很幸运一直在使用的框架，我想我可能会发布一个我发现自己反复使用的扩展的小集合。我希望下一个版本中会有一些这样的内容。

# 1.隐藏

这是一个视图修改器，允许您显示或隐藏通常无法显示或隐藏的视图。值得一提的是，视图修改器是一个非常有用的模式，你最好记住它。

这只是作为视图的修饰符，条件变量只是一个布尔值。

```
.modifier(Show(isVisible: condition))
```

这样做很好，但是正如你会注意到的，它确实释放了`view`上的空间，并强制进行重绘——因此影响了性能。或者，您也可以始终使用`opacity`选项卡来获得类似的效果。它会运行得更快，但不会释放您使用的空间。

# 2.树枝

我遇到的这个修改器是控制属性包含/排除的完美解决方案。

```
extension View {
  @ViewBuilder
  func `if`<Transform: View>(_ condition: Bool, transform: (Self) ->  Transform) -> some View {
    if condition { transform(self) }
    else { self }
  }
}
```

像这样使用的代码，也有一个变量，在这个例子中是彩色的。

```
.if(colored) { view in
  view.background(Color.blue)
}
```

# 3.打印

如果你是 SwiftUI 的新手，首先会抓住你的是`print`。它可能是调试技术的恐龙，但他们仍然在课堂上教授它，并且发现自己无法在 SwiftUI 视图中使用它是令人痛苦的。所以，这段代码是非常宝贵的。

```
extension View {
    func Print(_ vars: Any...) -> some View {
        for v in vars { print(v) }
        return EmptyView()
    }
}
```

它允许你在代码中使用这样的语句。

```
self.Print("Inside ForEach", varOne, varTwo ...)
```

# 4.耽搁

这在 iOS15 中有所改变，虽然不是 SwiftUI 本身，但这绝对是你在代码中发现自己在做的事情。

```
extension Task where Success == Never, Failure == Never {
  static func sleep(seconds: Double) async throws {
  let duration = UInt64(seconds * 1000_000_000)
  try await sleep(nanoseconds: duration)
  }
}
```

当然，您希望在延迟后运行的代码需要遵循创建延迟本身的`Task`。

```
Task { try! await Task.sleep(seconds: 0.5) }
```

# 5.passthrustubjects

当我开始在 SwiftUI 上使用`Combine`时，我发现`PassThroughSubjects`是一种连接新旧的超级有用的方式。我写了一篇关于在应用内购买中使用它们的文章，但是它们并不完美，当你发送它们时，我经常会触发不止一次。这段代码可以帮助您发现这个问题。

```
let changeColor = PassthroughSubject<Int,Never>()
```

您使用的代码如下所示:

```
.onReceive(signalButton
  .eraseToAnyPublisher()
  .throttle(for: .milliseconds(10), scheduler: RunLoop.main, latest: true))
{ value in
  if value == 2 {
    button2 = true
    levelColor = Color.red
  }
}
```

使用`calling`例程，在这种情况下，这里的数字用于让我命名一个主题，这样我就可以在同一个 SwiftUI 代码中触发多个分支。

```
changeColor.send(3)
```

# 6.捐款

开始代码的另一个替代方法，虽然不是 SwiftUI，但它是我在 SwiftUI 代码中反复使用的一个无价元素。您可以使用以下代码进行设置:

```
let cameraGesture = PassthroughSubject<cameraActions,Never>()var cameraSubscription:AnyCancellable? = nil
```

然后像这样使用两个`combine`声明:

```
cameraSubscription = cameraGesture
  .eraseToAnyPublisher().throttle(for: .milliseconds(10), scheduler: RunLoop.main, latest: true)
   .sink(receiveValue: { value in   
  // do something with the value                 
})
```

通过像以前一样发送一个`combine`消息。

```
cameraGesture.send(._1orbitTurntable)
```

# 7.计时器

一个`publisher`是一个创建计时器的好方法。我发现我几乎在我想到的每个项目中都在使用它。

```
let timer = Timer.publish(every: 1, on: .main, in: .common).autoconnect()
```

这是代码。

**注意**:我在`_ unused`变量中有时间值。与`send`不同，这种方式更可靠，因为它只发送一条信息，而不是一堆。

```
.onReceive(timer) { _ in
  // do something
}
```

# 8.坐标/尺寸

我不太记得我是怎么说的了，所以这里有一个例子。这也有点棘手，因为它返回的大小是父元素，而不是您在其中使用它的子元素。

所以，你通常用它作为背景视图。以下是如何使用此命令返回视图的大小:

```
.background(returnSize())
```

# 9.属性字符串

我在栈溢出时遇到的一个超级有用的扩展；它适用于 iOS 15 的`Text`对象中的属性。

您可以像这样使用上面的片段:

```
Text("GAME OVER") { $0.kern = CGFloat(2) }
```

有了这几种参数，你可以在这篇[文章](/ios-15-attributed-strings-in-swiftui-markdown-271204bec5c1)中详细添加。

# 10.任意视图

接下来是对抱怨不匹配视图的 SwiftUI 消息的修复。虽然我通常不鼓励使用`AnyView`，但有时候似乎也没有其他解决办法。

```
extension View {
    func eraseToAnyView() -> AnyView {
        AnyView(self)
    }
}
```

下面是一个扩展的例子，我想返回一个文本项或一个图像:

# 11.下标

这个扩展允许你给一个字符串加下标；它使用了一个已经存在很长时间的标准

您可以像在其他语言中一样使用代码。

```
let word = "Start"
  for i in 0..<word.length {
    print(word[i])
}
```

# 12.检测震动

我发现自己一直在查找这个谷歌代码。谁能记住这个序列？

您可以像这样创建扩展:

```
.onShake {
  print("stop it shaking")
}
```

# 13.拍摄视图快照

此宝石来自 [HWS](https://www.hackingwithswift.com) 网站；这是所有迅捷事物的绝佳来源。

你可以这样使用它:

```
let image = textView.snapshot().ignoresSafeArea
```

# 14.保存/加载图像

如果在苹果开发者门户上找到这个；它会派上用场。

在扩展中，您可以像这样使用代码:

```
@State private var image = UIImage(systemName: "xmark")!
private var url: URL {  let paths = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)  return paths[0].appendingPathComponent("image.jpg")}var body: some View { 
Image(uiImage: image)  
  .onAppear {   url.load(&image)  }  
  .onTapGesture {   url.save(image)  }
}
```

# 15.列出字体

快结束了。下一个很有用。

```
let fontFamilyNames = UIFont.familyNames
for familyName in fontFamilyNames {
  print("Font Family Name = [\(familyName)]")
  let names = UIFont.fontNames(forFamilyName: familyName)
  print("Font Names = [\(names)]")
}
```

使用 SwiftUI 代码，您可以找到如下字体:

```
struct Fonts {
  static func avenirNextCondensedBold (size: CGFloat) -> Font {
  return Font.custom("AvenirNextCondensed-Bold", size: size)
}
```

您将调用这个构造来使用代码:

```
.font(Fonts.avenirNextCondensedBold(size: 12))
```

# 16.三元运算符

这最后一个扩展可以用在 SwiftUI 的很多领域，但是我总是记不住语法。

这里，三元运算符计算`condition`和

*   如果`condition`为`true`，则执行`expression1`。
*   如果`condition`为`false`，则执行`expression2`。

三元运算符有三个操作数(`condition`、`expression1`和`expression2`)。因此，命名为三元运算符。

```
flipColor = flipColor == .blue ? .green : .blue
```

所以，如果`flipColor`是`blue`，我返回`green`，如果`flipColor`是`green`，我返回`blue`。

所有这些把我带到了这个列表的末尾。我希望你在这里找到了一些有用的片段。

感谢阅读！