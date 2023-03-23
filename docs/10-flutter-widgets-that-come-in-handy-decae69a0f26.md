# 10 个有用的小工具

> 原文：<https://betterprogramming.pub/10-flutter-widgets-that-come-in-handy-decae69a0f26>

## TextButton.icon、AnimatedSwitcher 等等

![](img/5cd3bb66fceab94630ea66384c84b884.png)

法希姆·蒙塔希尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

尝试学习一门新语言可能会令人害怕和厌倦。很多时候，我们希望我们知道早先存在的某些特征。在今天的文章中，我将告诉你最方便的 flutter 小工具，我希望我能早点知道。

# **垫片**

Spacer 创建一个可调整的空白空间，它占据 Flex 容器中部件之间的任何剩余空间，如行或列。

# **TextButton.icon**

这个小部件取代了在创建带有图标的按钮时使用行的需要。您必须提供图标和标签。

# **包裹**

它根据提供的方向值水平或垂直显示其子节点。它可以用来代替 Gridview。这个小部件反应灵敏，无需做太多就能适应不同的屏幕尺寸。

# **动画开关**

这个小部件用一个新的小部件来代替另一个小部件。它提供了一个很好的过渡，使应用程序非常流畅。总是给它的子部件添加一个键，以确保它正常工作。

# **安全区域**

这个小部件向你的小部件添加填充，确保你的应用程序不会与操作系统和设备显示功能(如状态栏)冲突。

```
SafeArea(child: Container())
```

# **刷新指示器**

将可滚动的小部件作为子部件。当孩子被过度滚动时，一个动画的循环进度指示器淡出视野，并调用一个未来更新可滚动的内容。

```
RefreshIndicator(
child: ListView(), 
onRefresh: () async {}),
```

# 丰富文本

这允许我们在同一个句子或段落上显示不同样式的文本。您可以包含内联链接、下划线文本、彩色文本等等。

# 改变

这个部件将你的动画游戏提升到一个全新的水平。它可以实现简单的动画，如旋转和缩放，以更复杂的，如三维和倾斜动画。它为快速实现提供了有用的命名构造函数，如 rotate、scale 和 translation。

# 交互式查看器

在小部件上引入缩放、平移、拖动和收缩功能的最简单方法。它可以根据您的需求进行高度定制。

# 流动

这个小部件利用变换的力量来交付很酷的动画。这是你必须看到的部件之一，才能理解它的力量。查看[官方文档](https://api.flutter.dev/flutter/widgets/Flow-class.html)了解更多信息。

# 芯片

这是一个简单的小部件，以有组织的方式漂亮地显示简单的数据。它有几个变体，如`InputChip`、`ChoiceChip`、`FilterChip` 和`ActionChip`。

```
Chip(   
avatar: CircleAvatar(     
backgroundColor: Colors.grey.shade800,     
child: const Text('AB'),   ),   
label: const Text('Aaron Burr'), 
)
```

各位，到此为止。感谢阅读。

现在你已经知道了一些非常酷的小部件，让我们开始为世界而建吧。