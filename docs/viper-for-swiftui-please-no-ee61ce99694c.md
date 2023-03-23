# 蝰蛇换 SwiftUI？求你了。号码

> 原文：<https://betterprogramming.pub/viper-for-swiftui-please-no-ee61ce99694c>

## 或者为什么你不应该抚摸那条特别的蛇。

![](img/286adae5b8cb667e384a33b37557bf3b.png)

克里斯·库里在 [Unsplash](https://unsplash.com/s/photos/viper?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

每个人都喜欢写他们如何通过 SwiftUI 使用他们最喜欢的 UIKit 架构。其中一个比较突出的设计模式/架构是 VIPER。

视图-交互者-演示者-实体-路由器。

不要误解我。VIPER 是一个很棒的软件架构，旨在解决在开发和创建基于 UIKit 和 UIViewController 的应用程序时遇到的许多经典和常见问题。

但是对于 SwiftUI 来说,*不是*最好的架构。也不是第二好的。事实上，这是最糟糕的情况之一，我希望在这篇文章中说明原因。

但在此之前，我们首先需要知道两件事。

1.  毒蛇是什么？
2.  它解决什么问题？

让我们开始吃吧。

# 毒蛇是什么？

如上所述，VIPER 是一个首字母缩写词，代表视图-交互者-呈现者-实体-路由器。

如果你正在读这篇文章，你可能已经熟悉了 VIPER。因此，与其花时间自己解释每一个组成部分，我将走捷径，从 Mahdi Chtioui 的一篇关于这个主题的写得很好的文章中借用一点。

以下是 VIPER 模块中的组件。

> **视图**:视图层，基本上是 UIViewController 和其他任何视图类型。这一层包含 UI 逻辑(显示、更新、动画…)，负责截取用户的动作并发送给演示者。最重要的是，它没有商业逻辑。
> 
> **Interactor** :我们可以把它想象成“网络管理器”:当提交者请求时，负责从服务(网络、数据库、传感器……)中检索数据。交互器负责管理来自模型层的数据(注意，模型不是 VIPER 架构的一部分，你可以随意实现它或不实现它，但肯定它会使我们的应用程序更加简洁)。
> 
> **演示者**:我喜欢把它想象成主板，它把所有层连接在一起。演示者是唯一与视图通信的层(其余层与演示者通信)。基本上，它是负责根据视图发送的用户动作做出决策的层。
> 
> 实体:实体只是我们的模型，由交互器使用。至少在我看来，最好将它们放在 VIPER 模块之外，因为这些实体通常由不同的模块在整个系统中共享。
> 
> **路由器:**有人称之为“线框”。这一层负责处理导航逻辑:推送、弹出、呈现 UIViewControllers。

VIPER 是最常见的所谓干净架构之一。但是为什么要使用它呢？

## 它解决什么问题？

VIPER 是传统 UIKit MVC(模型-视图-控制器)软件开发中最大问题的众多解决方案之一。

一个问题如此普遍，以至于 MVC 这个缩写词很快获得了另一个不同的、同样众所周知的定义。

> MVC:大规模视图控制器。

你知道问题所在。在 MVC 中，我们经常会把一个给定屏幕的所有代码放到那个屏幕的视图控制器中。标签和文本字段以及视图出口。按钮和动作处理程序。委托和事件处理程序。商业逻辑。验证。网络请求和错误处理。导航路由和控制。

真是一团糟。

VIPER 就是为了解决这个问题而创建的，它专注于一个核心概念，即单一责任原则。

以前包含在视图控制器中的所有代码都将被分解，并转移到一个特定的、定义良好的结构中。每个组成部分在这一过程中都有特定的职责和角色。

查看与演示者的对话。演示者与互动者交谈。Interactor 将实体返回给 Presenter，它将事物分解成视图可以使用的容易理解的部分。完成后，演示者与路由器对话，然后我们转到链中的另一个 VIPER 模块。

冲洗。根据需要重复。

如你所见，VIPER 创建了许多小的活动部件，不幸的是，为管理 VIPER 而编写的代码中有很大一部分是为了管理 VIPER 而编写的。

> 事实上，[我读过的一篇关于这个主题的文章](https://theswiftdev.com/the-ultimate-viper-architecture-tutorial/)指出，VIPER 创建并使用了如此多的样板代码，以至于人们应该依靠代码生成器来创建所有需要的东西。

但是……总之……它起作用了。

它可以工作…但是它不适合 SwiftUI。

为什么？首先，它是为另一个时代设计的。和另一种语言。和另一个 SDK。

## 委托

VIPER 起源于传统的 Objective-C 和基于早期 Swift UIKit 的开发，它显示了。

你看，VIPER 主要是通过委托来工作的。系统中的每个组件都有一个接口，为了让一个组件与另一个组件对话，它需要一个对该组件的引用。

创建并呈现一个新的 UIViewController 和 Presenter，其他所有东西都必须在一个正式的、严格的结构中创建、连接和绑定在一起。

视图控制器需要了解演示者，所以它有一个对它的强引用。但是演示者*也*需要知道视图控制器，所以它可以驱动 UI 的变化。因此，演示者需要一个弱委托链接返回到视图控制器(必须观察那些引用周期)。

VIPER 中的其他组件也是如此。

如果今天要求创建这样一个系统，人们可能会更关注具有回调闭包的函数，或者甚至使用 RxSwift 或 Combine 进行组件通信和消息路由，而不是硬连线委托。

但是这些工具不可用，或者不常用。没有多少人知道或使用 Rx，Objective-C 中的块语法非常糟糕[许多网站被创建](http://fuckingblocksyntax.com)来解释如何定义、创建和使用它们。

因此，委托被选为各层之间的主要通信机制。好吧。有点笨重，不过还好。我们应该可以接受。

## 没有风景

除了在 SwiftUI 中，视图不是视图。在 UIKit 中，UIView 或 UIViewController 是一种引用类型。我可以链接到它和它的 delgate。

在 SwiftUI 中，“视图”是定义我们的应用程序的轻量级结构。它们不是引用类型，而是值类型，因此演示者不能持有对值类型的引用。

在 SwiftUI 中，视图不是由演示者对视图的命令性调用来驱动的，以更新 UI。它们由系统驱动，基于可观察数据的变化。

在 VIPER 中，路由器隐藏了视图之间创建、推送和导航的复杂性。但是在 SwiftUI 中，导航就像在`NavigationLink`中包装一个视图，或者在`NavigationStack`中添加一个值一样简单。

因此，随着每一张行为多米诺骨牌的倒下，VIPER 赖以生存的整个结构和理性开始崩溃。它名副其实，变成了一个蛇窝，你越深入其中，就越有可能被咬。

我最近从一个代码库中看到了一些例子，在那里开发人员放弃了，并且*还*被迫在他的 VIPER 代码中添加一个视图模型，这样他就有地方放置他的可观察值了。所以，VIPERVM？

但是现在让我们忽略所有这些，因为 VIPER 有一个更大的问题。

你看，毒蛇很大程度上是一个错误问题的答案。

## 我们如何修复大规模视图控制器？

这样问的话，VIPER 似乎是一个合乎逻辑的答案。提取所有我们能提取的代码，并把它转移到其他地方。

公平地说，不管我们做什么，有些代码确实想放在别处。例如，网络代码。

但是这个简单的答案回避了真正的问题。

问:首先，为什么我们的视图控制器这么大？

想要答案？在这里。

***答:因为 UIKit 让构图很难。***

这就是问题的症结所在。

将 UIViews 和 UIViewControllers 分解成更小的组件是很困难的。太难了，事实上，我们不这样做。所以我们经常以一个单一的 UIViewController 来驱动我们在单一屏幕上看到的一切。

需要一些例子吗？

## UIKit 组件

想在你的屏幕上添加一个单独的`UIViewController`？你能不能只添加视图？只是添加视图控制器作为子视图？没有。你必须这样做。

```
let controller = AccountDetailsViewController()
controller.accounts = accounts
addChild(controller)
controller.view.translatesAutoresizingMaskIntoConstraints = false
container.addSubview(controller.view)
NSLayoutConstraint.activate([
    controller.view.leadingAnchor.constraint(equalTo: container.leadingAnchor, constant: 0),
    controller.view.trailingAnchor.constraint(equalTo: container.trailingAnchor, constant: 0),
    controller.view.topAnchor.constraint(equalTo: container.topAnchor, constant: 0),
    controller.view.bottomAnchor.constraint(equalTo: container.bottomAnchor, constant: 0)
])
controller.didMove(toParent: self)
```

想要使用在 XIB 中创建的界面元素吗？哦天啊。首先，我们必须让系统为我们做一个。

```
let cardView = NSBundle.mainBundle("CardView").loadNibNamed("", owner: nil, options: nil)[0] as! CardView
```

这假设你首先在`CardView`类中正确地实现了`required init?(coder aDecoder: NSCoder)`方法。

UIKit 鼓励你写代码来创建视图，写 xib 来创建视图，写故事板来创建视图。但是想一起用？将一个嵌入另一个？那是痛苦开始的地方。

UIKit 通过要求特殊情况下的特殊视图使问题变得更加复杂。需要表格视图的标题吗？嗯，那不是`UIView`，那是`UITableViewHeaderFooterView`。想在列表中显示视图吗？当然是`UITableViewCell`。或者可能是`UIContainerViewCell`，如果你是那样滚动的话。但是嘿！想在另一个视图中使用刚刚编写的单元格视图吗？没有。

UIKit 主动使用*来对抗*视图合成。

当系统积极地与解决方案对抗时……我们最终会做得更少，这有什么奇怪的吗？

但是如果不是这么痛苦的话。如果将我们的视图和视图控制器分解成更小的视图和视图控制器更容易些…

*首先，我们的大规模视图控制器会如此庞大吗？*

## *作文*

*我已经写了这个[再](https://medium.com/swlh/structural-decomposition-in-swiftui-8892e512b18e)和[再](https://medium.com/swlh/deep-inside-views-state-and-performance-in-swiftui-d23a3a44b79)。SwiftUI 基于的基本原则之一是*组合*。*

*想在我们的视图中嵌入在别处创建的 CardView 吗？*

```
*VStack {
    ...
    CardView(title: "Account", item: account)
    ...
}*
```

*嘣。就这么简单。想在列表中使用吗？*

```
*List {
    ForEach(accounts) { account in
        CardView(title: "Account", item: account)
    }
}*
```

*完成了。*

*正如我在[SwiftUI 组合的最佳实践](/better-programming/best-practices-in-swiftui-composition-282b02772a24)中指出的，如果在 WWDC swift ui 会议期间有一件事被反复重复，那就是 swift ui 视图是极其轻量级的，创建它们几乎没有性能损失。*

*想把视图的一部分分割成一个更小的子视图吗？又快又简单。见鬼，在 Xcode 中，你只需按住 command 键点击部分代码，然后选择“提取子视图”。*

*现在，您可能已经看到了这一趋势…*

## *视图-交互者-演示者-实体-路由器*

*如果在 SwiftUI 中创建和使用小的、专用的、特殊用途的视图和组件很容易…那么我为什么要使用一个*不鼓励我这样做的架构呢？**

*一个架构需要我移动视图代码…和相关的表示器代码…和相关的交互器代码…以及其他所有需要的东西。*

*请注意，前面提到的 SwiftUI VIPERVM 示例有超过 **200 行**的 VIPER 样板代码…全部支持**一个**20 行的视图。*

*两百行代码。*

*因此，如果每个新视图*也*需要创建大量额外的类和组件来配合它…我会在需要时创建许多简单的子视图吗？或者我应该避免麻烦，继续将所有内容整合到更大的 SwiftUI 视图中？*

*我要用大规模视图体来代替大规模视图控制器吗？*

*我们得到 VIPER 是因为 UIKit 不鼓励视图合成。*

*但是如果毒蛇也和我们作对…*

## *斯威夫特伊*

*正如我在 [SwiftUI:选择应用架构](/swiftui-choosing-an-application-architecture-6ec9289f8e8f)中总结的:*

> *SwiftUI 为编写应用程序带来了一种简单的声明式方法。也许更确切地说，是*简洁*。你可以用很少的代码完成很多工作。*
> 
> *因此，给我们自己和我们的应用程序增加过于正式的架构的负担将是一种耻辱，这又一次显著地增加了我们需要编写的代码量。*

*我们当然不需要为不同的 SDK 设计的架构，构建和设计来解决和克服另一种语言和另一种环境的限制。*

*我们需要一个架构，让 SwiftUI…成为 SwiftUI。*

## *完成块*

*原来如此。在我看来，VIPER 虽然仍然是 UIKit 开发的好选择，但对于 SwiftUI 来说却是一个极其糟糕的选择。*

*但是用什么代替呢？嗯，你可以在 [SwiftUI 中看到我目前的想法:选择应用架构](/swiftui-choosing-an-application-architecture-6ec9289f8e8f)。*

*关于这件事我还有一些想法。*

*但那是另一篇文章了。又是一天。*

*在那之前，让我知道你的想法。我说的对吗？错了？*