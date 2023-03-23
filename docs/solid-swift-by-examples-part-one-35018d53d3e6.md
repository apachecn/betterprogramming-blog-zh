# 稳固和单一责任原则

> 原文：<https://betterprogramming.pub/solid-swift-by-examples-part-one-35018d53d3e6>

## swift 实例中的坚实原则

## 坚固的雨燕

![](img/4608618fab57ada7f8b077596c5e563e.png)

伯纳德·赫曼特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们所有在某种程度上沉浸在软件开发中的人都听说过、经历过、有过想法，或者有过坚实的原则。这些原则在几种编程语言中已经被描述和使用了很长时间。

这些概念对于所有正在开发软件的人，或者那些必须从质量和功能的角度审查代码片段的人，例如审查其他编程语言代码的开发人员，仍然是有用的。不管你从事软件工作有多长时间，这些原则作为模式在开发时为好的决策提供信息。

让我们看看这些模式是什么，它们试图解决什么。

# 糟糕的架构决策

让我们试着确定你是否已经有了坏代码。

你是否曾经试图修改某段代码，并注意到你将不得不花费大量的时间来重构或改变功能/文件/类，而不是特定的代码？

这个问题被称为*刚性*，因为它需要很多努力，比如改变项目的几个部分。

除了特定的一段代码之外，您是否曾经更改过一段代码并在功能/文件/类中出现错误/崩溃/问题？

这个问题被称为*脆性*，因为一个变化可能会打破意想不到的部分。

你曾经尝试过在另一个文件/模块/项目/存储库中重用一段代码，并且不得不添加依赖关系吗？

这个问题被称为*不可移动性，*因为在另一个文件/模块/项目/存储库中重用一个组件是困难的，因为它有太多的耦合依赖。

我把所有这些问题总结成一条黄金法则:

**编写易于删除或替换的代码。**

正如鲍勃大叔(罗伯特·c·马丁)所说，这些概念并不是规则、法则或完美的真理。这些是帮助你开发好软件和提高软件架构质量的指导方针。

# 固体

SOLID 是首字母缩写，代表五个指导原则。在接下来的几篇文章中，我将用 Swift (iOS)中的例子来解释每个原则。

单一责任原则认为一个函数、模块、类或结构应该只有一个目标、理由或任务。每当你创建/改变一个函数/模块/类/结构时，你应该问自己:它有多少职责？听起来容易对吧？

我们通常违反这一原则，因为应用这一模式在您需要为其创建的文件、类和/或协议的数量方面可能是一项艰巨的工作。

在我作为 iOS 开发人员的经验中，我看到了我们通常违反单一责任原则的最糟糕的元素之一——视图控制器。

考虑到以下因素，我们很容易发现这个问题:

*   视图控制器可以是数据源，也可以是表视图或集合视图的委托。
*   您可以在视图控制器中发出 web 请求。
*   您可以在视图控制器中使用业务逻辑。
*   您可以在视图控制器中格式化元素。
*   您可以在视图控制器中处理导航。

让我们看一个例子:

一个基本的大视图控制器，用于列出电影

乍一看，一点也不差。然而，这个类不仅仅完成一项工作；正如你在函数名中看到的，这个类试图实现不同的目标。这是糟糕设计的标志。

这个班有多少责任？

`MovieListViewController`首先从 API 检索数据，然后解析 API 响应，然后过滤电影列表以显示即将上映的电影，最后重新加载显示单元格的表格，格式化单元格标题。此外，我们可以使用 IBAction 过滤列表，并在单元格被点击时显示`MovieDetailViewController`。

实现这一原则的一些关键是使用协议委托、设计模式(如 Presenter 模式)、和代码隔离或组合(将责任向下移动到小类)。

让我们来重构一下:

同样的 VC，现在看起来更好

因此，首先，我们已经隔离了视图职责，只使用协议(`MovieListView)` **、**委托(`MovieListTableViewDataSource)` **、**和 MVP 模式( `MovieListPresenter)`)在视图中显示信息。

然后:

一些隔离责任的其他类

我们已经移动了表数据源作业，创建了一个类来处理所有需要的表工作和通过委托(`MovieListTableViewDataSource)`)进行的表视图选择。

最后:

我们的演示者、API 和解析器

我们已经创建了一个`MovieListPresenter`，在这里我们可以处理业务逻辑，并使用`MoviesAPIClient`类从 API 获取数据。我们使用`MovieParseHandler`解析响应。

我们在这个示例项目中做了很多改进。这可能需要时间，因为您需要分离所有的类作业，并为其创建大量文件，但是您将获得更好的、完全可测试的代码。

# 结论

这些原则帮助你尽可能保持课堂整洁。不要忘记，您将需要使用良好的实践和设计/架构模式作为工具来应用这些概念，并将您的代码提高到下一个级别。

希望你喜欢这篇文章，并找到它的帮助。感谢阅读。请在下面的评论中告诉我你的反馈。

# 下一章

[](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) [## 开闭原理

### 在第一部分，我们描述了什么是坚实的原则和他们试图解决什么，解释了第一…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) 

# 参考

[](https://www.scaledrone.com/blog/solid-principles-for-becoming-a-better-ios-swift-developer/) [## Scaledrone 实时消息服务—博客

### 虽然 SOLID 可能已经过时了(至少在互联网时代是这样)，但它是那些超越…

www.scaledrone.com](https://www.scaledrone.com/blog/solid-principles-for-becoming-a-better-ios-swift-developer/) [](https://clean-swift.com/single-responsibility-principle-for-class/) [## 班级单一责任原则——干净利落

### 您看到了依赖性反转原则是如何成为一个好的清理的核心指导原则的，尽管它并不为人所知…

clean-swift.com](https://clean-swift.com/single-responsibility-principle-for-class/) [](https://sites.google.com/site/unclebobconsultingllc/getting-a-solid-start) [## 有了一个坚实的开端。—清洁编码器

### 鲍勃叔叔咨询有限责任公司。

sites.google.com](https://sites.google.com/site/unclebobconsultingllc/getting-a-solid-start) [](https://codeburst.io/solid-design-principle-using-swift-fa67443672b8) [## 使用 Swift 的可靠设计原则

### 坚实的原则是实现/创建有效的、可维护的、可扩展的和松散的…

codeburst.io](https://codeburst.io/solid-design-principle-using-swift-fa67443672b8) [](https://github.com/ochococo/OOD-Principles-In-Swift#-the-single-responsibility-principle) [## ocho coco/OOD-Swift 原则

### 💎基于 Bob 叔叔文章的 OOD (SOLID)原则。ochococo/OOD-Swift 原则

github.com](https://github.com/ochococo/OOD-Principles-In-Swift#-the-single-responsibility-principle)