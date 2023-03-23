# SwiftUI:矩形

> 原文：<https://betterprogramming.pub/swiftui-rectangle-61e782daa283>

## 和圆形一样，SwiftUI 中的矩形和圆形非常简单

传统上，您将创建一个`UIView`来创建一个`Rectangle`。使用 SwiftUI，只需调用它的方法就可以创建一个矩形。

您应该在这里看到 SwiftUI 为您完成所有次要工作的模式。

在本教程中，您将学习如何在 SwiftUI 中从头构建一个矩形。

# 先决条件

按照本教程，你需要一些基本知识。

*   对 Swift 的基本熟悉。
*   至少 Xcode 11

# 矩形

有两种类型的矩形可供您使用。一个有圆角，一个没有圆角。

```
Rectangle()
    .fill(Color.green)
    .frame(width: 300, height: 100)
```

![](img/4c1dfa0eaa4b3b888f0afd7eac4f848e.png)

```
RoundedRectangle(cornerRadius: 4.0)
    .fill(Color.green)
    .frame(width: 300, height: 100)
```

![](img/8b98f84cdcfd3479b69ebab6041457f4.png)

请注意两个矩形之间的差异。要使用带圆角的，你必须使用`RoundedRectangle`。

如果背景颜色不合你的口味，你可以试试`stroke`。

```
.stroke(Color.green)
```

![](img/df956b9c1927243fa8c639ed92410246.png)

您甚至可以配置笔画的宽度。

```
.stroke(Color.green, lineWidth: 4.0)
```

![](img/0311d7ca8812709e59e13a4a00cc61b9.png)

虚线呢？

```
.stroke(Color.green, style: StrokeStyle(lineWidth: 4.0, lineCap: .round, dash: [10, 20]))
```

![](img/5f7fb17d338a4d63a9571e6a86c9a9d5.png)

让我们在你的项目中尝试一些更实际的东西。以`RoundedRectangle`为背景的按钮怎么样？

![](img/71c11265a2b290eaefa402709e905ee7.png)

除了文本，图片呢？

![](img/eaedcbe49d37c7a6fec34bf727218c76.png)

如果你属于中风类型。

![](img/06d85b6728ef830a2373e687f8d289b6.png)

感谢阅读！