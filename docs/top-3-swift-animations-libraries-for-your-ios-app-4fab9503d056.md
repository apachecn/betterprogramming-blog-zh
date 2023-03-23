# 适用于您的 iOS 应用程序的三大 Swift 动画库

> 原文：<https://betterprogramming.pub/top-3-swift-animations-libraries-for-your-ios-app-4fab9503d056>

## 让我们来看看这些库，改进你的应用程序的 UX，让它更有趣

![](img/d6b1b9a7e084b54af9f6b5a0545f22af.png)

照片由[亚伦·伯顿](https://unsplash.com/@aaronburden?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/three?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

当你回顾 2007 年左右，当 iOS 和 iPhone 发布时，应用程序都是一样的:相同的 UI 界面，相同的行为，相同的功能；App store 只是一个大容器，里面装着计算器、实用程序以及诸如此类的东西。10 多年后的今天，一切都变了。

应用程序需要一个非常酷和性感的用户界面，现代的颜色，渐变，大多数时候，甚至是一个非常专注的使用方式，将你的产品与大众区分开来。在一个拥有数百万应用的市场中，你的应用需要有新意，才能击败你的竞争对手。动画区是一个好的开始，让一些东西变得特别——让一些东西看起来时髦。

另一方面，动画很难编码，需要付出没人愿意付出的努力。开发人员关注设计模式、Swift 5 特性和软件组件。他们真的不想浪费时间用经典的`UIView.animate()`编写动画。这就是为什么你可以集成一些很酷的库来把这个活动变得更简单——甚至更有趣。

# 洛蒂:一种精神状态和一个未讨论的图书馆

Lottie 是一个由 Airbnb 开发的流行库，它支持 Adobe After Effects 动画，并且能够将它们转换为原生 iOS 动画。更有甚者，洛蒂将该库移植到了 Android、web、React Native、Windows 上，成为了最跨平台的动画库。

在 GitHub 上有超过 18，000 颗星星，Lottie 是一个强大的、有良好文档记录的库，可以集成支持模型，制定各种各样的设置，利用大量的类来支持行为，并为自己提供控制动画的能力。

[](https://github.com/airbnb/lottie-ios) [## Airbnb/洛蒂 ios

### Lottie 是一个 Android 和 iOS 的移动库，它可以实时渲染基于矢量的动画和艺术…

github.com](https://github.com/airbnb/lottie-ios) 

# Hero:制作过渡和视图动画的流行库

Hero 是另一个您可能想考虑为您的 modals 和 UIViewControllers 添加味道的库。

Hero 基本上能够在 UINavigationController 和内容上方弹出的控制器中导航时实现多个过渡。

Hero 的编码很好，它在 Git 上已经存在很多年了——这就是为什么它可以被认为是一个可靠的库来激活你的应用程序。

最后，考虑使用 Hero even 来制作 UICollectionView 或 UITableView 的动画:它集成了对 UITableViewCells 和 UICollectionViewCells 的支持，可以在各种布局中重新排列和显示它们。

而且，17000 颗星也不是随便的壮举。

[](https://github.com/HeroTransitions/Hero) [## 英雄过渡/英雄

### Hero 是一个用于构建 iOS 视图控制器过渡的库。它在 UIKit 的基础上提供了一个声明层…

github.com](https://github.com/HeroTransitions/Hero) 

# 春天:简单有效

如果 Lottie 是为专业人士设计的，而 Hero 可能看起来很难，那么 Spring 是一个非常有用的库，可以在 UIViewController 上制作任何 UIView 动画。

我个人使用过很多次 Spring，我发现它非常强大。想象一下，你希望你的 UIButton 在你的视图上浮动，或者你希望你的按钮在被点击后挤压或晃动。无论哪种情况，这个图书馆都适合你。

您可以在 GitHub 页面上找到一个非常好的骨架应用程序，它演示了所有动画，并提供了各种选项来定制效果的定时、放松和力量，因此您可以在将动画轻松集成到您的项目之前，在演示应用程序上尝试动画。

春天用它的一万三千颗星星展示肌肉。

[](https://github.com/MengTo/Spring) [## 蒙多/春天

### 需要 Xcode 10 和 Swift 4.2。将 Spring 文件夹拖放到 Xcode 项目中(确保启用“拷贝项目，如果…

github.com](https://github.com/MengTo/Spring) 

# 周围的其他东西和最后的考虑

网络上和 GitHub 上有很多库可以制作动画元素，但这三个库肯定是最受欢迎的。

除此之外，你还可以找到各种各样的小库来制作 UIButtons，ui buttons 动画，以及制作酷炫的刷新控件，提醒，祝酒词等等。

这篇文章展示了三个顶级框架，但是如果你有时间并且你需要在你的应用程序上制作一个东西或者一个特定元素的动画，你应该考虑一个特定的库。

最后一点:不要忘记将你的实现建立在可负担的库的基础上。有各种各样的小的和被窃听的回购会给你带来更多的问题而不是好处。