# 在 SwiftUI 中处理颜色

> 原文：<https://betterprogramming.pub/handling-colors-in-swiftui-576e8f100d65>

## SwiftUI 的引入带来了另一个颜色类，名为 Color——但是不要太舒服

![](img/6c6d48266d967194aa0273db3609c6b0.png)

罗伯特·卡茨基在 [Unsplash](https://unsplash.com/s/photos/colors?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

经过多年与众多颜色类(`UIColor`、`NSColor`、`CGColor`等)打交道。)*，*任何一个 Swift 开发者在看到这个统一的颜色类别的第一眼时，都可能会感到无比的喜悦。最后，前进的道路似乎是免费的繁琐的工作转换颜色！

但是不要太舒服——将 SwiftUI `Color`类转换成它的一个更老的表亲并不是一件简单的任务。当您需要将 SwiftUI 颜色传递给一个只接受`UIColor`的方法时，您可能会发现自己正在尝试一个非常混乱的转换计算。我自己也绞尽脑汁试图改变一个`UITextField`可表示的文本颜色。

我敢打赌，你是在经历了类似的失败后阅读这篇文章的。

# 最简单的解决方案:颜色资源

通过将颜色保存为颜色资产，我们可以通过字符串值来访问它们——使颜色的转换只需用资产名称初始化所需的颜色类。

使用资产名称初始化颜色

将颜色存储为颜色资源在开发中一直有几个优点，但它们通常更多是事后才想到的。

Apple 不鼓励将颜色存储为原始 RGB/HSB 值，而是提倡使用颜色资源。

我的猜测是，上述原因是为什么在 SwiftUI 中，如果没有繁重的工作，访问`SwiftUI.Color`组件似乎是不可能的。但是如果你一定要将`SwiftUI.Color`转换成另一个类，目前唯一可用的方法是将颜色描述(如果是十六进制字符串)转换成 RGB 分量。下面是一个 iOS 扩展，它完成了对`UIColor`的转换:

对于 macOS，只需将`UIColor`替换为`NSColor`。

将颜色转换为用户界面颜色

请注意，上述扩展不适用于系统颜色(如`SwiftUI.Color.red`)，因为`color.description` 将不包含十六进制字符串值，而仅包含名称*红色*。