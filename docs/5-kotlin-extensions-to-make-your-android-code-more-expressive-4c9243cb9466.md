# 5 个 Kotlin 扩展让你的 Android 代码更有表现力

> 原文：<https://betterprogramming.pub/5-kotlin-extensions-to-make-your-android-code-more-expressive-4c9243cb9466>

## 提取包参数，验证字符串，等等

![](img/f5da8253ed3dbd35446356540e725656.png)

万尼亚·马蒂耶维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

您可能已经阅读了大量关于 Kotlin 扩展的文章，但本文并不全是关于扩展的。这是为了让你的代码更有表现力，所以我把重点放在解释和包括我的顶级扩展上，这些扩展尽可能地保持代码的自然。

主要目标是学习如何通过使用扩展的概念，而不是复制粘贴代码片段，使您的代码以您自己的方式表达。

# 介绍

Kotlin 是一种现代的、富有表现力的语言，是为开发人员设计的。在我看来，好的 Kotlin 代码只不过是能够以自然、可读的方式表达自己的代码。

从 Java 切换到 Kotlin 对我来说在很多方面都是一个巨大的转变，但我要说这在每个方面都是最好的。你可以在[我之前的文章](https://medium.com/better-programming/my-journey-from-java-to-kotlin-3bfcbcc6b734)中读到所有关于它的内容。

我最喜欢 Kotlin 的一点是扩展的概念。作为一名移动 Java 开发人员，我从未考虑过向任何类添加定制功能的能力——尤其是第三方库中的类。但是当我听说扩展的概念时，我大吃一惊。对于 Android 开发人员来说，这个特性打开了许多代码增强的大门。

> Kotlin 提供了用新功能扩展一个类的能力，而不必从该类继承或使用 Decorator 之类的设计模式。这是通过称为扩展的特殊声明来实现的。”— [科特林的文档](https://kotlinlang.org/docs/extensions.html)

要了解关于 Kotlin 扩展的更多信息，请阅读以下文章:

[](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb) [## 使用 Kotlin 进行高级 Android 编程

### kot Lin-您应该使用的独有功能

medium.com](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb) 

我使用 Kotlin 扩展使代码更具表现力，并尽可能保持语言的自然性。

别再耽搁了，让我们开始吧。

# 1.显示、隐藏和删除

移动开发人员的一个常见任务是隐藏和显示视图。如果使用 Java，需要根据需求调用`setVisibility`函数并传递`View.VISIBILE`或`View.GONE`。看一看:

```
view.**setVisibility**(View.GONE)
```

这段代码没有错。它工作了。但是使用`set`和`get`函数让它看起来更笨拙而不自然。Kotlin 提供了一种无需使用`set`和`get`函数的现成方法来赋值。现在看起来是这样的:

```
view.visibility = View.GONE
```

即使是现在，因为赋值操作符，它看起来也不自然，所以我想，“为什么我不能使用扩展使它尽可能自然呢？”那时我开始使用以下扩展:

现在我们可以如下所示使用它:

```
view.**show()**
view.**hide()** 
view.**remove()**
```

现在看起来更好——更自然。我很想优化它，所以如果你有任何建议，请留下评论。

# 2.确认

在任何开发环境中，验证字符串都是至关重要的。回到 2015 年，当我开始我的职业生涯时，我看到一些应用程序在一些文本字段中显示`null`。这是因为没有适当的验证。

在 Kotlin 之前，我会维护一个实用程序类，并包含一些静态函数来验证字符串。看看 Java 中一个简单的验证函数:

本质上，数据类型只不过是类。所以我们可以使用 Kotlin 扩展来添加验证功能。例如，我创建了以下数据类型`String`的 Kotlin 扩展来检查它是否有效:

嗯，对我来说，`data.valid()`似乎比`Utility.isStringNotNullOrEmpty(data)`更干净，可读性更强。调用数据类型的扩展函数似乎比触发一些实用函数更自然。下面是几个扩展，可以激励你编写自己的验证扩展:

# 3.提取束参数

在 Android 中，我们通过一个包——一个键值对——在组件之间传递数据。通常，在从包中检索数据之前，我们必须检查一些事情。首先，我们应该检查我们要找的密钥是否在包中。然后我们需要检查它是否有有效值。通常，我们通过以下方式实现:

这涉及到更多的手动代码，坦率地说，看起来并不美观。想象一下，如果有五个参数，代码看起来会有多乏味。正如我所说的，代码应该尽可能的自然，并且包含最少的手工工作。

这里，我使用了四个扩展函数:两个用于活动，两个用于片段。同样，每个组件一对，以获得非空值或可空值。看一看:

要了解像内联函数和具体化类型这样的高级特性，请阅读本系列文章[。](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)

现在让我们看看如何使用上面的扩展:

这种方法有三个优点:

1.  干净，可读，最少的代码。
2.  空安全值。
3.  懒惰的执行。

# 4.资源扩展

在 Android 中，我们需要通过资源类访问项目级资源。这涉及到一些样板代码，每次我们想从资源文件中检索数据时，都需要手工编写这些代码。在没有任何扩展的情况下，检索颜色或 drawable 如下所示:

当试图检索任何资源时，您需要通过生成的 R 文件中的资源 ID 来访问它。ID 的数据类型为`Int`。因此，我们可以为每个资源类型编写 integer 类的扩展，并在调用站点使用它们来减少样板代码并使其可读:

# 5.显示警告对话框、Toast 或 Snackbar

当涉及到前端开发时，不管是什么平台，都会有需要向用户显示警告的时候。它可能是用于不重要的数据或警告对话框的 toast，用于确认或显示一些错误。

当你想显示一个简单的祝酒辞时，你需要写的语句可能会很长。我甚至不打算进入警告对话框。这些是常见的任务。它们应该简洁并且易于实现。这就是为什么我使用下面的扩展使代码在调用点尽可能的简洁:

编写这些扩展是一次性的工作。在呼叫站点查看这些分机的用法:

常见的任务应该尽可能容易实现、易读和自然。

# 奖金

要了解更多关于协同程序和 Kotlin 的其他高级特性，请阅读以下文章:

*   [“面向 Android 开发者的 5 款 Kotlin 扩展”](/5-more-kotlin-extensions-for-android-developers-3857b1f16407)
*   [“来自 KTX 库的 5 个强大的 Kotlin 扩展”](/5-powerful-kotlin-extensions-from-ktx-libraries-b10c50504df6)
*   [“使用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)”
*   [“科特林协程，从基础到高级](https://medium.com/better-programming/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)”
*   [“使用新 Kotlin 流进行异步数据加载](https://medium.com/better-programming/asynchronous-data-loading-with-new-kotlin-flow-233f85ae1d8b)”
*   [“为什么以及如何使用 Kotlin 的原生序列化库](https://medium.com/better-programming/why-and-how-to-use-kotlins-native-serialization-library-c88c0f14f93d)”

目前就这些。希望你学到了有用的东西。感谢阅读。