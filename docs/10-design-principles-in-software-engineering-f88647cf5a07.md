# 软件工程中的 10 个设计原则

> 原文：<https://betterprogramming.pub/10-design-principles-in-software-engineering-f88647cf5a07>

## 节省你自己的时间和麻烦

![](img/fa793592a0944de5a3cc7b54045a7203.png)

马文·迈耶在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

有多少次你想给你的应用程序增加一个新的特性，但是因为它太死板而不能实现？有多少次你不得不重写代码以使其可测试？有多少次你不得不添加更多的代码来使你的代码最适合移动设备？

这一切都归结于设计原则。在这篇文章中，我将分享所有软件工程师/程序员需要知道的十个设计原则。

这些原则与如何开发系统设计、如何实现特定组件以及如何编写代码有关。它们不是你赖以生存的东西。它们只是你在设计或实现系统时需要考虑的指导方针。换句话说，如果你决定遵循它们，它们会让你未来的生活变得容易得多。

# 1.各个击破

在任何解决问题的情况下，包括系统设计，核心原则之一是分而治之。它意味着将一个问题分解成更小的子问题。这背后的想法是，这些问题由于其复杂性而难以解决。为了简单起见，你可以把这些问题分成更小的问题。从长远来看，解决这些小问题会更容易解决大问题。

在编码场景中，您可能有一个包含一些子系统的系统，这些子系统包含不同的包，这些包包含使用一些外部依赖项或一些函数的不同方法的类。我刚刚通过写下这个声明，把整个系统分成了几个小节。如果所述的子部分有问题，你会发现在类中使用的方法是否有问题，然后上升到不同的类并检查它们是否是问题，然后上升到包，然后到子系统。在你意识到之前，你已经解决了问题。

把它想象成一个俄罗斯娃娃类型的情况。

# 2.增加凝聚力

内聚意味着将有意义的东西组合在一起——就像一个包。从开发的角度来看，您可以选择内聚性地设计您的包、模块或类。例如，想想 Python 中的数学包。数学包是内聚的，因为它包含了所有与数学运算相关的东西。你在里面找不到任何非数学的东西。

内聚性带来了你的代码的组织，它将使寻找东西变得容易得多，从而简化了系统。一切都会变得更有意义。这样，你就不用去字典包里找机器学习相关的函数之类的东西了。

# 3.缩径管接头

简单地说，当包、模块、类或文件非常相互依赖时，就会发生耦合。我不知道你怎么想，但我知道这不是任何系统的最佳实现。为什么？如果一个包发生了一些变化或损坏，整个系统都会受到影响，因为它的某些部分依赖于出现故障的包。

我意识到，拥有一个高度耦合的系统在开始时更容易实现，但是如果任何组件出现故障，调试和修复将会困难得多。

通常，您的系统高度耦合，以至于很难确定耦合发生在哪里。您可能有一个包依赖于另一个包，而另一个包又依赖于另外八个包。你知道这会有多大问题吗？你可能处于这样一种情况，当隐藏在四级耦合下的另一个包可能出现故障时，你不知道为什么一个包不工作。我希望你明白这个想法。

如果你能使你的组件尽可能的独立，你将会有更容易的调试时间。那是一个更好的设计。

# 4.增加抽象

如果某个东西是某个技术的简化版本，那么它就更加抽象。从编码的角度来说，越抽象的东西越一般化。让我用一个例子来说明这一点:假设您有一个函数，它只接受一个三角形作为输入，并将其显示在屏幕上。如果你有不同的形状显示在屏幕上会发生什么？你必须为每一个形状写一个函数。如果有一个函数可以接受任何形状，并根据其属性显示特定的形状，那么抽象会更好。这将是一个更抽象的设计。

在这个例子中，三角形是一个形状的具体实现，而形状是一个一般的概念。抽象减少了代码重复，同时增加了安全性。

# 5.提高可重用性

这非常简单直观，但是我们希望确保无论何时我们编写代码，我们都在考虑如何使代码尽可能可重用。它与抽象密切相关。与其写一个特定的函数做好一件事，但可能只在一个特定的实现中起作用，我们可以把它一般化——更抽象一点。这样，我们允许在不同的上下文中重用。这是可重用性背后的主要思想。

把它想象成预先做一个小小的牺牲来节省以后的时间，而不是必须回去理解这段代码，重写它，或者做另一个函数。

# 6.灵活性设计

灵活性设计归结于对未来系统变化的预测。你的系统现在可能很简单，但将来会变得更复杂。你可能想添加更多的东西。您可能希望用一个更好的实现来替换一个对象或项目的实现。很多时候，人们设计系统是为了今天的使用。他们没有考虑到这样一个事实，在一个月或者一年后，他们会扩大这个项目的规模。

对于开发者来说，最糟糕的事情莫过于重新开发他们之前已经开发过的东西，但是我们都必须面对自己的错误来学习。在这种情况下，用新特性修补代码库不会有什么效果。开发一个全新的设计比试图修改现有的设计更容易。我相信你以前也经历过这种情况。

# 7.预见过时

如果您在项目或系统中使用外部依赖项，您需要非常小心您正在使用的依赖项。它们将来会过时吗？有许多不同的方式可以让外部依赖逐渐被遗忘。它可能会被弃用，在更新后可能不再适用于特定版本的编程语言，或者可能在 Windows、Linux 或 Mac 上不受支持或维护。你明白了。

你需要确保你没有一个依赖于成百上千个外部依赖的项目，因为如果其中一个出了问题，你将有一段时间笑不出来。为了安全起见，避免软件的早期发布，使用信誉良好的公司的软件，将外部依赖的使用保持在最低限度，并远离文档记录不良或维护不良的项目。

# 8.便携性设计

当设计一个系统时，你需要记住它可能会在一个不同于你当前目标的平台或设备上使用。如果你只是在为网络制作一个网络应用程序，如果你想把它变成一个 iOS 应用程序、Android 应用程序、Windows 桌面应用程序或类似的东西，那将是昂贵和耗时的。这可能需要创建一个全新的系统来移植。

所以，在设计系统时，你要记住这一点。

# 9.可测性设计

这是几乎每个开发人员都会犯的错误，因为我们都是从小项目开始的。你应该知道，在大型系统和大型代码库上工作时，可测试性设计变得非常重要。想想像谷歌或微软这样的公司。你觉得他们一天要做多少测试？更好的是，您认为他们对每个拉取请求运行了多少测试？可能在 5000 左右。为什么？他们需要确保你提交的任何东西都不会损坏。

他们是如何进行这么多测试的？编写这些大规模代码库的开发人员为他们提供了一种方法，使他们能够测试他们编写的东西。要点是，如果你在一个巨大的代码库中写了很多代码，确保它是可测试的。

# 10.防御性设计

没有办法轻松地做到这一点，但这仅仅意味着对你的代码进行白痴检验或者外行检验。你甚至可以把它看作是对你的代码的初学者验证。这是我能解释这件事的最好方式。

你必须想象任何使用你的代码、框架、类或任何东西的人都是初学者。你得想象一下。你必须确保你有好的错误信息。您必须确保您正在处理任何人扔给它的所有无效输入。让它变得强大。确保它有有效的错误消息和可能的通知，以帮助用户正确使用它。

如果你开始摆弄任何语言的语法，你会得到相当好的错误信息。你会知道你做错了什么，如果你在 Google 或者 Stack Overflow 上查找，你会发现你在程序中做错了什么。你能做到这一点的唯一原因是，编写代码的人把它设计成防御性的。他们知道像你和我这样的人会使用这个框架或语言，他们设计它的方式可以帮助我们使用它。

# 结论

就像我开头说的，这些原则不是你总需要遵循的东西。这些原则也有例外。这里的主要思想是你需要意识到这些原则。想想他们。如果你想打破其中一个，确保你能证明为什么，并理解为什么这个原则可能不适用于你的情况。

希望这对你有帮助。