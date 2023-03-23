# 如何用提供者管理 Flutter 中的状态

> 原文：<https://betterprogramming.pub/how-to-manage-state-in-flutter-with-provider-661ff322dd22>

## *一个关于 Flutter 最受欢迎的国家经理的三集系列*

![](img/6ef165780c9d8816befad208c0beeb3a.png)

照片由 [Fabian Albert](https://unsplash.com/@fabiraw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/mobile-device?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Flutter 是一个很酷的新人——或者说现在已经十几岁了。Google 的 UI 套件使你能够从单一代码库构建本地的高性能移动和网络应用。除此之外，大量可用的[小部件](https://flutter.dev/docs/development/ui/widgets/material)带来了快速而愉快的开发体验。

被 Flutter 的潜力所吸引，我最近一头扎进了我的第一个 Flutter 项目。我的热情没有消退。Flutter 兑现了它的所有承诺。但是新工具提供的机会也带有一些风险。

摆弄新玩具时，阅读说明书通常是个好主意。由于 Flutter 只是在最近几年才开始流行，它的使用手册还在编写中。

清晰简洁的指南会让我的第一个 Flutter 项目变得更好。尤其是为国家管理选择正确的工具绝不是一件容易的事情。选项很多，但它们之间往往缺乏明确的比较。

在我看来，国家管理者之间的比较是非常必要的。一旦你的应用程序的状态与一个状态管理器纠缠在一起，切换到另一个工具就不是一件容易的事情了。因此，为了帮助您第一次就正确理解，我将在一个由三部分组成的系列中比较 Flutter 最受欢迎的状态管理器。

*   *第 1 部分:您是否需要一个状态管理器来管理提供者的状态*

## 你需要一个州经理吗？

在开始比较各种状态管理器选项之前，您应该问自己:

"T15 我甚至需要一个状态管理器吗？"

如果您的应用程序在设计上非常扁平，并且状态很少在窗口小部件树中上下移动超过一级，那么通过属性和回调传递状态可能会为您节省很多样板文件。

在下面的例子中，`Parent`向`Child`传递一个回调，以跟踪`TextButton`被点击的次数:

然而，如果您发现自己通过部件树的多个级别传递回调，那么以这种方式管理状态很快就会变得麻烦和混乱。在这种情况下，可以肯定您需要一种更强大的方式来管理状态。但是什么才是适合这项工作的工具呢？

## 使用[提供者](https://pub.dev/packages/provider)管理状态

在 Flutter 应用程序中，有大量的[选项](https://flutter.dev/docs/development/data-and-backend/state-mgmt/options)来管理你的状态。供应商是最受欢迎的州经理之一。这个社区创建的工具依赖于三个核心概念:

1.  `ChangeNotifier`:存储您的状态，从该状态开始更新状态，并通知使用该状态的微件。
2.  `ChangeNotifierProvider`:这个小部件使得树中的底层小部件可以访问`ChangeNotifier`。
3.  `Consumer`:监听状态变化并相应更新 UI 的小部件。

下面是一个使用提供者模式更新点击计数的示例:

*   状态由存储`_count`的`DataProvider`类存储和处理。
*   通过点击按钮调用`IncrementCount`，从`Child`小部件更新`_count`。
*   `NotifyListeners()`确保通过`Consumer`访问状态的`Parent`小部件得到相应的更新。

那么，Provider 是状态管理的最佳工具吗？

**优点**

*   维护良好且久经考验的套件。
*   单向状态管理的完整工具包。

**缺点**

*   总共有令人印象深刻的九个不同的提供商，要正确理解提供商并不容易。
*   不必要的重建不容易避免。默认情况下，`Consumer`会更新它所有的子部件，即使状态中不相关的部分已经被更新。
*   `Provider`依赖于 Flutter SDK，让你的业务逻辑和框架密不可分。这在建筑设计中被认为是一种不好的做法。
*   状态更新不是基于事件的。所以在上面的例子中，我们知道`_count`已经被更新，但是我们只能猜测改变的起源。这使得跟踪和理解应用程序中的状态变化变得复杂。

综上所述，我认为`Provider`对于*经验丰富的* Flutter 开发者来说是一个很好的选择，他们正在构建一个具有简单状态需求的应用。`Provider`易于学习但难以掌握，此外，缺乏更复杂应用程序所需的基于事件的状态跟踪。

在本系列的下一部分中，我将使用 BLoC 模式研究状态管理。

# 资源

*   扑:【https://flutter.dev/】T4
*   Flutter 的 widget 目录:[https://flutter.dev/docs/development/ui/widgets](https://flutter.dev/docs/development/ui/widgets)
*   Flutter 的状态管理选项:[https://Flutter . dev/docs/development/data-and-back end/state-mgmt/options](https://flutter.dev/docs/development/data-and-backend/state-mgmt/options)
*   提供商包:[https://pub.dev/packages/provider](https://pub.dev/packages/provider)
*   九种不同类型的提供者:[https://pub . dev/documentation/provider/latest/provider/provider-library . html](https://pub.dev/documentation/provider/latest/provider/provider-library.html)