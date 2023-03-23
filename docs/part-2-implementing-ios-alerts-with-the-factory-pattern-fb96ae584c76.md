# 使用工厂模式改进我们的 iOS 警报实现

> 原文：<https://betterprogramming.pub/part-2-implementing-ios-alerts-with-the-factory-pattern-fb96ae584c76>

## 识别和理解设计模式

![](img/183dc6f78757c1dc689877e4b8ff777d.png)

照片由[凯特琳·贝克](https://unsplash.com/@kaitlynbaker?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你没有读过“用工厂模式实现 iOS 警报”的第 1 部分[，这里有链接](/a-better-way-to-implement-alerts-on-your-swift-ios-projects-70de896bcdc2)。

此外，请使用我的例子作为一个什么是可能的公会。代码可以无限改进，也可以无限变化。换句话说，总有更好的方法来编写代码。

另外，我想提一下，命名我的变量、类、结构等对我来说一直是个挑战，我还不是首席工程师，所以我也有很多东西要学。并不是说首席工程师是完美的，什么都知道，但这是我目前在自己的职业发展中努力的目标。

够了，我不想在不同模式的定义上浪费太多时间，所以我将只提到一些我认为有助于解释每个模式是什么的链接。我将解释我如何使用它们，如何识别它们，以及它们的好处。

## 工厂模式的资源

以下是我推荐的一些关于工厂模式的阅读材料:

*   [https://www . raywenderlich . com/books/design-patterns-by-tutorials/v 3.0/chapters/11-factory-pattern](https://www.raywenderlich.com/books/design-patterns-by-tutorials/v3.0/chapters/11-factory-pattern)
*   [https://stevenpcurtis . medium . com/the-factory-pattern-using-swift-b 534 AE 9 f 983 f](https://stevenpcurtis.medium.com/the-factory-pattern-using-swift-b534ae9f983f)

## 构建器模式的资源

以下是我推荐的一些关于构建器模式的阅读材料:

*   [https://www.geeksforgeeks.org/builder-design-pattern/](https://www.geeksforgeeks.org/builder-design-pattern/)
*   [https://www . tutorialspoint . com/design _ pattern/builder _ pattern . htm](https://www.tutorialspoint.com/design_pattern/builder_pattern.htm)

查看[这个故事，了解更多关于工厂与建筑商模式的信息](https://medium.com/javarevisited/design-patterns-101-factory-vs-builder-vs-fluent-builder-da2babf42113)

# 在我的警报实现中识别这些模式的用法

工厂模式

注意我的`AlertImplementation`只做一件事。它创建了一个`UIAlertController`。我可以使用这个构建函数来创建一个新的`UIAlertController`。

> 工厂协议将返回新的对象。

如果你使用 Swift 阅读了工厂模式，你会读到这句话。然而，正如我之前所说，代码是可以无限改进的。

例如现在看它，我应该调用它来创建，而不是调用这个函数 build，因为它总是创建一个新的`UIAlertController`。

建设者

我的工厂构建函数包含一个`AlertViewData`。这就是我如何使用构建器模式的。构建器通常有 setter 函数和 getter 函数来组装新对象。

然而，在 Swift 中，我们不使用 getters，尽管我没有 setter 函数，但我有这些项目需要在每次构建新的`AlertViewData`时进行设置。

> ***构建器**创建一个**产品**，就像**工厂**所做的那样。但是现在，不是只有一个创建产品的请求，而是有几个对构建器中的方法的调用。每次电话都会给产品添加一个新的零件。当构建器被提供了所有需要的东西后，可以调用它的方法 *get()* 来用构建器之前收到的元素组装新产品。*

*哈维尔·冈萨雷斯很好地解释了它们的区别，并举例说明了它们是如何被使用的，因此，我在这里联系了他。所以，如果你还没有关注他，就给他一个关注吧。*

*有建筑商的工厂*

*当把它们放在一起时，你可以创建一个单独的`alertFactory`来使用任何你想要的`AlertViewData`构建器。上面是如何使用它的一个简单的例子。*

# ***使用这些模式的好处***

*如果你阅读了我上面链接的要点，你就已经知道了使用每种模式的好处。*

*但是我们可以更进一步，将它与其他模式结合起来。例如，当你将它与一个`ViewModel`和一些服务结合在一起时，你会惊奇地发现你可以用它做什么。*

*信号服务*

*这里我们有一个`AlertService`专门用于登录应用程序。我们可以使用我们的构建器在这里构建各种警报，而不需要实际使用我们的工厂方法，直到我们准备好显示它们。*

*一些方法和变量已经从下面的代码中删除，所以如果你只是复制和粘贴，而只是作为我如何使用它的一般指南来阅读，它可能不会工作。*

*SignInViewModel*

*`ViewModel`将使用并初始化我们的`SignInAlertService`来决定在验证时显示哪些错误警告。*

*下面是一个使用单独的`SignInView`作为视图的控制器。在这里，我练习使用控制器作为`ViewModel`和视图之间的媒介。这有助于减少这些`ViewControllers`中的行数。*

*信号控制器*

*`SignInController`将初始化一个`AlertFactory`,当我们需要从我们的 ViewModel 的委托方法创建一个新的警报时，将会用到这个`AlertFactory`。*

*现在我们已经将数据从视图中分离出来，并使用单个`alertFactory`实例来创建基于`SignInAlertService`的`AlertViewData`的多个警报。*

*如果您有任何问题或者您认为有更好的方法，请告诉我。*

*我把这个留给你。学会快速编写 iOS 应用程序！*