# 在 SwiftUI 中使用新的 iOS 15 DynamicTypeSize

> 原文：<https://betterprogramming.pub/using-the-new-ios-15-dynamictypesize-in-swiftui-8e8e860aebde>

## 让您的文本和视图可以使用不同的字体大小

![](img/cb60b028134bf4fdb008b0b9d5e66431.png)

由 [CardMapr](https://unsplash.com/@cardmapr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

早在 2019 年 8 月，我就向自己介绍了动态文本大小的世界。我所在国家流行的送餐应用在改变系统字体大小时没有改变文字大小。(他们仍然没有)我想知道增加文本大小和相应地改变视图需要什么 API。

在 SwiftUI 中，使用系统字体文本样式会自动更改:

```
Text("Something cool goes in here.")
  .font(.body)
```

当尺寸增大或减小时，它还会相应地调整视图和约束。但是，当您将文本大小增加到可访问性大小时(巨大的文本)，视图可能会被截断，看起来不可读。您可能需要创建另一个不同的视图结构来解决这个问题。

# 旧方法—内容大小类别

SwiftUI 为我们提供了一个环境`ContentSizeCategory`。下面是我的应用程序中的一个例子，当大小增加时，我将布局从网格更改为列表:

```
struct HomeGameTypeView: View {
  [@EnvironmentObject](http://twitter.com/EnvironmentObject) var preferences: Preferences

  [@Environment](http://twitter.com/Environment)(\.sizeCategory) private var category

  var body: some View {
    if category >= .extraExtraLarge {
      ScrollView {
        LazyVStack {
          list
        }
      }
    } else {
      LazyVGrid(columns: [.init(), .init()]) {
        list
      }
    }
  }

  var list: some View {
    ForEach(GameTypeOption.allCases) { type in
      HomeGameTypeRow(selection: $preferences.gameTypeSelection, type: type)
    }
  }
}
```

# 新方法——动态规模

在 iOS 15 中，`ContentSizeCategory`被弃用，取而代之的是`DynamicTypeSize`。重写上面的例子:

```
struct HomeGameTypeView: View {
  [@EnvironmentObject](http://twitter.com/EnvironmentObject) var preferences: Preferences

  [@Environment](http://twitter.com/Environment)(\.dynamicTypeSize) var size // <- NEW

  var body: some View {
    if size >= .xxLarge { // <- NEW
      ScrollView {
        LazyVStack {
          list
        }
      }
    } else {
      LazyVGrid(columns: [.init(), .init()]) {
        list
      }
    }
  }
}
```

这也可以将文本大小限制在一个特定的大小(我认为不推荐)或一个大小范围内。

```
var content: some View {
  Text(item.name.uppercased())
    .font(.title)
    .dynamicTypeSize(..<DynamicTypeSize.accessibility1) // <- RANGE
    .foregroundColor(.primary)
}
```

我知道标题文本样式足够大，我不希望它超出可访问性中等大小。

# 预览中的动态大小

要在预览中测试不同的大小，您可以将恒定大小作为环境添加到预览中:

```
struct IntroductionView_Previews: PreviewProvider {
  static var previews: some View {
    IntroductionView()
      .environment(\.dynamicTypeSize, .accessibility1) // <- CONSTANT
  }
}
```

# 大型内容查看器

如果要限制文本大小，可以添加方法`accessibilityShowsLargeContentViewer()`。当用户使用辅助功能文本大小时，在视图上长按会显示放大的文本大小:

```
var body: some View {
  Button(action: action) {
    content
  }
  .buttonStyle(HomeButtonStyle(isSelected: false))
  .padding([.bottom, .horizontal], 8)
  .accessibilityShowsLargeContentViewer()
}
```

尽管如此，请注意文档中的这些文字:

只有在由于不可避免的设计限制而必须保持小项目时，才依赖大内容查看器。例如，标签栏中的按钮保持较小，以便为应用程序的主要内容留出更多空间。

不要使用大型内容查看器来取代适当的动态类型支持。例如，动态类型允许列表中的项目垂直增长或收缩，以适应用户的首选字体大小。只有在由于不可避免的设计限制而必须保持小项目时，才依赖大内容查看器。

# 结论

动态类型可帮助您的用户使用任何文本大小的应用程序，为他们提供卓越的体验。使用 SwiftUI 可以更容易地添加动态类型并相应地构建视图。

我知道随着视图变得越来越复杂，这变得越来越困难，但是你仍然可以尽你最大的努力使你的应用程序至少可以被`xxxlarge`大小的用户访问。

```
**Want to Connect?**If you have a better approach, let me know [on Twitter](https://twitter.com/rudrankriyam)!
```