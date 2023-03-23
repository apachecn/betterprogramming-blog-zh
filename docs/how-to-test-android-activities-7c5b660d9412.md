# 如何测试 Android 活动

> 原文：<https://betterprogramming.pub/how-to-test-android-activities-7c5b660d9412>

## 在测试环境中，Android 是不存在的。这意味着测试即使是最简单的活动也会很棘手

![](img/c6648145587c8acecf061107864b3544.png)

[Pim Chu](https://unsplash.com/photos/dWzWo22F0mA) 在 [Unsplash](https://unsplash.com/) 上拍照。

当我开始开发 Android 应用程序时，我没有编写任何测试。当然不是——我只是在混日子，做些自己喜欢的东西。然后，我决定成为一名专业人士。我从小处着手，用最简单的应用程序和最简单的测试，很快就被难住了。对于任何同病相怜的人来说，这篇文章是给你的！

假设您有一个带按钮的活动。当您按下按钮时，您想要打开不同的活动。

您的源代码可能如下所示:

在布局文件的某个地方，你将一个按钮的`onClick` 设置为`openOtherActivity`。但让我们只关注科特林代码。没必要把目标定得太高，对吧？

然后(或者最好是在此之前，如果你正在努力应用 TDD ),你试着写一个测试。首先，您可能甚至没有检查它是否如您所期望的那样运行。最简单的测试就是“当你调用函数时，没有任何东西爆炸。”

它可能看起来像这样:

然而，即使这个非常简单的测试也会导致错误:

```
java.lang.RuntimeException: Method startActivity in android.app.Activity not mocked.
```

这是因为在测试环境中，Android 特有的东西并不存在。这是 Android 深思熟虑的设计选择，有利也有弊。这个单元测试决定的一个结果是我们必须模仿 Android 特有的组件。

Kotlin 的一个模仿库是[mock](https://mockk.io/)，我将在这个例子中使用它。任何其他类似的模仿库应该可以做到这一点。

所以，回到我们的测试。我们可以使用来自 MockK 的`spyk` 来模仿`startActivity`方法(并保留其他所有真实的东西):

现在测试通过了！

从那里，我们可以扩展测试来更彻底地检查函数的实际行为。例如，它是否以正确的意图调用`startActivity` 。

在下一个例子中，假设我们想在开始活动之前添加一些额外的内容:

我们尝试运行一个类似的简单测试，只是检查没有任何东西爆炸:

我们已经使用了与上次相同的嘲讽技巧，但是现在我们得到了另一个错误:

```
java.lang.RuntimeException: Method putExtra in android.content.Intent not mocked.
```

这看起来与上一个错误非常相似。然而，我们不能用同样的方法修复它。

我们无法访问意图，因为它是在活动内部创建的。这意味着嘲笑意图是很棘手的。

解决这个问题的一个方法是注入一个工厂对象，我们可以模仿它。

工厂对象需要一个函数，我们可以用它来创建意图，而不用直接调用构造函数。它可能看起来像这样:

然后，我们需要将该工厂注入到活动中，这样我们就可以在测试中覆盖它。

依赖注入可以在 Android 中通过使用类似 [Dagger](https://dagger.dev/) 的框架来实现。但是我们不需要这样做——从一个简单的实现开始就很好。我们可以将依赖项作为可选参数传递给活动，就像这样:

运行时，`ExampleActivity` 将用默认值创建，这将创建一个新的真实意图。不过，现在我们可以在测试中将它覆盖为一个模拟对象:

注意，放置`relaxed=true` 给出了一个 mock，它默认为每个方法调用返回 mock。

瞧啊。一个 Android 活动的单元测试，但是所有 Android 特有的东西都被安全地嘲笑了。

![](img/2d92a2c61ad16b95fc57eb71410de301.png)

[奥斯汀·施密德](https://unsplash.com/@schmidy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/s/photos/happy-woman-silhouette?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。