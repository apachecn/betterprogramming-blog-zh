# 斯威夫特的单身族

> 原文：<https://betterprogramming.pub/singleton-in-swift-8da9bea06339>

## 确保你有一样东西！

![](img/d2752f7279288ea896d2de89680fc2b7.png)

泰勒·拉斯托维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

难度:初级|简单**|**普通** |挑战**

作用域是编程语言中某些东西的可见性。一种让某些东西到处可用的方法是简单地创建一个全局变量或方法。

然而，如果您想要更复杂的东西—也许是一个可以在许多地方访问的类—您可能会遇到一个问题。如何确保在应用程序的不同位置(或不同的线程)使用相同的引用？这就是单身族的用武之地。

如果您想跳到 Swift 中的单件示例，请跳到视频

# 先决条件

*   能够产生一句“你好，世界！”iOS 应用程序(参见本指南[)。](https://medium.com/@stevenpcurtis.sc/your-first-swift-application-without-a-mac-79598ad839f8)
*   对警卫有所了解(见[这篇文章](https://medium.com/@stevenpcurtis.sc/precondition-assert-fatal-error-or-guard-in-your-swift-code-5f9297658be0))。
*   [初始化器](https://medium.com/@stevenpcurtis.sc/swift-initializers-fc12908a9106)的隐含知识。

# 术语

*   类:在 Swift 中定义的对象，使用按引用传递语义。
*   设计模式:软件设计中常见问题的解决方案。
*   实例:某事物的一个例子或一次出现。
*   实例化:特定对象的创建。
*   Singleton:保证一个类只创建一个实例的一种实例化方式。

# 作为设计模式的单例

singleton 是一种设计模式，它确保一个类只有一个实例，并且无论您在哪里尝试访问该类，您都可以访问该类的 singleton 版本。

这是一种广泛使用的解决一个类的多个实例问题的方法，它通常被称为设计模式。

事实上，它被如此广泛地使用，以至于苹果自己在类似 [URLSession.shared](https://developer.apple.com/documentation/foundation/urlsession/1409000-shared) 的类中使用它(注意，苹果称之为共享单例会话对象)。

## 线索就在名字里

单例需要是唯一的、单一的实例。

为此，初始化器应该是私有的，以防止客户创建他们自己的 singleton 对象的实例(因为那样的话，你知道，它就不是 singleton 了)。

即使单例只能创建一次，但如果多个线程试图一次实例化单例又会怎样呢？这会带来很多麻烦，所以我们需要确保我们的代码是线程安全的。

# 记录在案的单身者(谢谢，苹果)

## 简单的独生子

在文档中，苹果有一种方法来创建单例:

## 具有属性的单例

然后他们继续回答这个问题:有方法和属性要设置的单例怎么办？

## Apple 的 singleton 与属性的实现

当我们实现这个的时候，我们需要对初始化更加小心一点——我们*只*希望我们客户端的用户使用共享实例，永远不要创建他们自己的 singleton 实例(记住，实例应该是单数)。

这个实现给了我们一个计数器，这意味着我们可以看到什么时候`incCounter()`被点击。

注意初始化器是私有的，所以它不能被这个类的错误用户调用(因为这是一个单例，这应该是唯一的一个类)。

您希望在控制台上打印什么内容？

当你考虑这件事的时候，我会等一会儿。

明白了吗？

是的，是`4`，因为计数器从 1 开始计数，然后有三个电话打到`incCounter()`。而且由于这是一个单例，所以如果你试图使用同一个实例的不同“实例”也没关系。

## 防止子类化

有一个危险，这个危险就是 Singleton 的公共 API 可能被误用。关键字`final`防止类被子类化，这意味着任何类都不能从它继承，并且类的行为不能被类的用户改变。

这是通过在声明类之前编写`final`关键字来实现的:

它可以放在本文中任何一个类的前面，使它也成为`final`。

那似乎很好。他们甚至推荐使用单例或网络管理器来发出 HTTP 请求。

你看到可能的问题了吗？如果没有，你当然应该继续读下去。

# 创建网络管理器来发出 HTTP 请求

## 基本原理

网络管理员将使用`URLSession`来创建网络请求。

为了简单起见，这个简化的网络管理器将只通过一个方法向`URL`发出一个简单的请求，该方法要求网络管理器发出那个请求。

重点是我们想要创建一个可以被*嘲弄* 的对象来进行测试。

## 使用依赖注入

`URLSession`已经是 singleton 了，是进行网络调用的对象。这可以从主类中抽象出来，所以可以将`URLSession`注入到网络管理器中，或者用作模拟类。完整的实现有点复杂(有几个协议围绕着这个实现)，要看到这一点，请查看完整的 [HTTPManager 类](https://medium.com/@stevenpcurtis.sc/my-basic-httpmanager-in-swift-db2be1e340c2)。

这里是一个相当简单的演示(如前所述，如果你感兴趣的话，看看完整的类可能是有益的)。

代码(在提供的测试中)给出了一种以简化的方式模拟网络调用的方法。

# 结论

你实现应用程序的任何方式在业内其他人看来都可能是对或错的。

然而，在我们的军械库中，单件衣服是“小心使用”的工具。苹果公司确实使用了它们，但是您可能也想在您实现中使用依赖注入，这也是事实。

适合您工作的工具？那是你的决定。

当你在那里的时候，为什么不看看[回购](https://github.com/stevencurtis/SwiftCoding/tree/master/Singleton/Code)中的代码呢？

# 扩展你的知识

*   存储的属性被苹果记录为单例。
*   我有一个[完整的基本 HTTPManager](https://medium.com/@stevenpcurtis.sc/my-basic-httpmanager-in-swift-db2be1e340c2) 类，你可以看看。您还可以看到依赖注入在实践中是如何实现的。