# 探索 Google Play 的应用内评论 API

> 原文：<https://betterprogramming.pub/exploring-google-plays-in-app-review-api-8cd8586d26c5>

## 谷歌提供了一种应用内的系统范围的功能

![](img/7cabe3e90166bbee60ed496734e67e70.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Jonas Leupe](https://unsplash.com/@jonasleupe?utm_source=medium&utm_medium=referral) 拍摄。

这是一个指南，解释如何集成新的谷歌应用内审查 API，这是谷歌 Play 核心库的一部分。您还将学习如何使用 FakeReviewManager 测试 API。

# 介绍

谷歌已经努力了很长时间来提供 API，这将使他们能够访问 Android 开发者，以触发系统范围的功能，如自动填充 OTP、应用内更新等。现在是应用内评论的时候了。

对于像 Android 这样的系统，不可避免地会期望更多的定制和更好的应用内体验。谷歌几年前开始注意到这一点，并开始构建应用内更新等 API。

这些 API 为开发人员提供了执行全系统功能的访问权限，而无需用户离开应用程序(应用内体验)。从长远来看，这些类型的改进将提高系统的质量。

进入今天的主题，谷歌的应用内审查 API 将让开发者触发一个评级底单，用户可以在这里对应用进行评级并提供详细的审查。用户不再需要导航到 Play Store 来给出评论。

现在我们有了一个应用内评论 API，下一个问题是什么时候要求用户评论。这是许多开发人员纠结的最棘手的问题之一，因为在正确的时间要求评审是至关重要的。

这取决于应用程序的功能和类型。以下是关于这件事的一些注意事项:

1.  当用户启动应用程序时，不要要求查看权限。
2.  当用户正在执行任务时，不要用弹出评论来打断他们。
3.  只有当你确定用户已经对你的应用/游戏有足够的体验时，才要求评论。

是时候集成 API 了。

# 综合

正如我所说，从 1.8.0 版本开始，应用内评论 API 是核心 Google Play 库的一部分。首先，我们需要在应用程序级`build.gradle`文件中的 dependencies 标签下包含下面一行:

```
**def play_core_version** = '1.8.0'
implementation "com.google.android.play:core:$**play_core_version**"
```

# 创建 ReviewManager 实例

我们需要通过`ReviewManagerFactory`创建`ReviewManager`接口实例。利用这一点，我们可以开始一个应用内评论流程。我们必须用一个`addOnCompleteListener`来观察反应。

如果请求成功，那么我们可以检索`ReviewInfo`对象。必须记住，这个对象只在有限的时间内有效。因此，开发人员只需要在确定显示应用内审核流程时调用`ReviewManager`实例上的`requestReviewFlow`。

现在，让我们看看如何创建`ReviewManager`实例并调用`requestReviewFlow`:

从 ReviewManager 获取 ReviewInfo 数据。

# 显示检查对话框

现在我们有了`ReviewInfo`，是时候调用评审流程了。这只能在我们从上一节的`ReviewManager`中获得有效的`ReviewInfo`时执行:

触发审核流程。

`addOnFailureListener`和`addOnCompleteListener`是请求的回调。

# 测试

现在我们已经完成了实现，是时候进行测试了。我们可以用内部测试跑道来测试。最重要的是应用要发布在 Play Store 的内测赛道，用户要安装。

或者，我们可以使用来自应用内审查工件的`FakeReviewManager`实现进行测试，它允许您伪造 API 的行为。要使用`FakeReviewManager`，请用`FakeReviewManager`实例替换`ReviewManager`实例:

```
val manager = **FakeReviewManager**(context)
```

# 定额

为了给用户提供良好的体验，Google Play 实施了一些限制(例如，它显示查看对话框的频率)。目前还不清楚 Google Play 如何衡量这一点，因为它还处于早期阶段。但是我发现，当我在 24 小时内多次尝试请求审查流时，审查对话框没有显示。

也不建议使用行动号召来触发评审流，因为用户可能会在之前调用它。

# 结论

尽管 API 还处于早期阶段，但我已经看到许多开发人员在他们的产品级应用中使用它，并获得了显著的正面评价。我建议现在就使用它。我确信随着 API 的发展，开发人员最终会有更多的控制权。

目前就这些。希望你学到了有用的东西。感谢阅读！