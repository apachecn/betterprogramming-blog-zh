# 选项不是可选的

> 原文：<https://betterprogramming.pub/optionals-are-not-optional-2259b42d2b13>

## 用 Swift 定义对象的存在

![](img/1f32d08248024c07c15f4d3d06c82c67.png)

照片由[艾米丽·莫特](https://unsplash.com/@emilymorter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

编程语言(或任何语言)最难处理的概念之一是对象不存在。Swift 使用术语`nil`来表示一个对象的缺失。从表面上看，这似乎没有太大的意义，但是深入挖掘会发现开发人员在使用这种有创意的语言时可能会遇到的无数陷阱。如果您试图以危险的方式访问该对象，会发生什么？你怎么知道一个对象可能为零？当使用它时，你如何确保它不是零？

使用 Swift，解决方案是可选的。定义一个对象时，可以通过在类型中添加一个`?`或`!`来表示它是可选的。以《指环王》中的角色咕噜姆为例。在故事的某些时候，他有戒指，而在其他时候，他没有。如果我们正在创建一个 Gollum 结构或类，我们可以使用选项来模拟这个场景。

我们的咕鲁结构有一个名为`theRing`的变量，它是一个可选的`TheOneRing`对象。当咕噜拥有戒指的控制权时，它会被设定一个值。当他没有的时候，`theRing`就是`nil`。

![](img/cfe4ad3b6149231ded5a25806eca70c9.png)

照片由[通讯社跟随](https://unsplash.com/@olloweb?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我们开始使用期权之前，让我们仔细看看它们是什么。可选的只是一个有两种情况的枚举:`.some`和`.none`。当你的可选持有一个对象时，`.some` case 有一个关联值，当它不持有时，`.none` case 表示你的可选是`nil`。这意味着您可以像处理 Swift 中任何其他 enum 一样处理可选值。使用上面的`theRing`示例，它看起来像这样:

这只是一个针对`theRing`对象的 switch 语句。如果它有一个关联值，那么它将属于`.some`情况，并且`theRing`将被打印；如果没有，那么它将属于`.none`情况，并且`Nothing here`将被打印。

# 展开选项

![](img/e162f3b9f68a6a45e31df177d59181c7.png)

[Erda Estremera](https://unsplash.com/@erdaest?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

Swift 的一大优点是，这一切都是通过一个称为解包的过程为您处理的。在深入研究如何打开选项之前，让我们从一个新的例子开始。由于许多人将软件开发等同于魔术，我们将使用魔术师进行魔术表演。首先，我们定义`Magician`类型:

在我们的例子中，有两件事情定义了一个`Magician`:他们可能使用或不使用道具，他们有能力变戏法。接下来，我们将创建一个采用可选`Magician`参数的函数:

![](img/5e4d6fafd054a2b41b99e4731276b427.png)

由 [Jose Aljovin](https://unsplash.com/@josealjovin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

因为魔术师是可选的(用`?`表示)，我们必须打开它，看看它是否有价值。最简单的方法之一是使用`?`操作符本身。让我们看看如何使用我们的`attemptTrick`函数。

就是这样！如果`magician`参数包含值，那么该对象被展开，其`doTrick()`函数被调用。如果取而代之的是`nil`，那么当`attemptTrick(with: Magician?)`被调用时，什么都不会发生。

`?`操作符的另一个用途是[可选链接](https://docs.swift.org/swift-book/LanguageGuide/OptionalChaining.html)。假设您有一个可选类型，其中嵌套了另一个可选类型，并且您想要访问嵌套可选类型的属性。我们的魔术师类型有一个可选的道具类型，现在让我们用一个可选的属性`isAvailable`来定义道具类型，它将模拟道具是否可用。

现在我们可以使用可选链接从我们的魔术师那里获得`isAvailable`属性。

注意，函数的返回类型是可选的 bool。这是因为如果魔术师或道具都没有值，函数将返回零。

不过，这没什么意义。道具要么是可用的，要么是不可用的，从来没有真正的时候，它可以是其他任何东西。让我们使用一种叫做`if let`的新技术来解开我们的选项并清理函数。当使用`if let`语法时，你基本上是在说“如果我能让我的对象成为我正在解包的东西，就把代码放在花括号里”。它遵循与任何其他 if 语句相同的逻辑类型。要记住的一点是，你展开的对象只在 if 语句的范围内可用，它不存在于花括号之外。

我把这句话理解为“如果我能让道具等于魔术师的道具，则返回真，否则返回假”。这个函数现在不仅每次都返回一个实际值，还意味着您可以通过消除`isAvailable`属性来清理 Prop 类型。

# 使用 Guard 语句

![](img/b471245c00851759f85c82eb7277f036.png)

安德烈·伯奇科在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

虽然`if let`很不错，但我更喜欢使用另一种叫做 guard 语句的解包技术。Guard 语句的优点是在语法上稍微容易阅读，并且允许您的未包装对象在更大的范围内可用。下面是我们使用`guard`的`isPropAvailable`函数。

到目前为止，我们只在代码中使用了`?`操作符，现在让我们看看它的孪生兄弟`!`操作符。使用`!`展开可选件被称为强制展开。它本质上意味着你知道你的选择有一个值。事实上，你非常确定它有价值，如果它没有价值，你愿意冒险让你的应用崩溃。这就是使用`!`的后果:如果做得不好，你的应用就停止运行。你可能会问自己“如果后果如此严重，我为什么要使用它？”。如果您正在编写一段代码，并且希望在继续之前绝对确定您的应用程序处于某种状态，该怎么办？如果它没有处于正确的状态，崩溃可能比继续运行更好。以下是我们更新后的强制解包示例:

这个例子不可否认是开玩笑的。如果你看到魔术师表演的大楼着火了，你最担心的就是魔术师不在那里表演魔术。

# 代替零

![](img/61d63c8d2d86931d8fb79f7f5e07cee1.png)

由[吉尔博特·易卜拉希米](https://unsplash.com/@jilburr?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我想介绍的最后一个可选性是零合并操作符`??`。这个操作符允许你用一个对象代替 nil。想象一下我们的魔术师生病了，有一个替角代替他参加活动。看起来是这样的:

这个函数将接受你第一个选择的魔术师，但是如果魔术师返回 nil，它将创建一个魔术师类型的新实例并返回它。你将永远有一个魔术师来表演！

# 包扎

![](img/466bdd136ef59afe69cf0590313fbf8d.png)

Ezra Comeau-Jeffrey 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

有些人认为选项是语言中最难理解的部分。当我刚开始学习 Swift 时，他们确实让我感到困惑。我来自一个没有可选方案的 Java 背景。相反，您学会了在使用值之前检查`null`。另一种选择是崩溃。在我看来，可选性是 Swift 相对于 Java 等语言的最大优势之一。是的，有一个与它们相关的学习曲线，但是精通它们的使用将对你如何编写应用程序产生实质性的影响。