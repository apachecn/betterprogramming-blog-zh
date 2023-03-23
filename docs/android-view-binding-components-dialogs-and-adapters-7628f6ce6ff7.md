# Android 视图绑定组件:对话框和适配器

> 原文：<https://betterprogramming.pub/android-view-binding-components-dialogs-and-adapters-7628f6ce6ff7>

## 不再有 findViewById。是时候升级了

![](img/083bef4b8fbb207e6d442b72ab90d5fb.png)

丹尼尔·冯·阿彭在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

今天，您将学习如何使用视图绑定来链接视图和代码，而不是传统的`findViewById`或重量级的数据绑定框架。在我以前的文章中，我们讨论了基础知识。在本文中，我们将讨论 Android 组件，如对话框和适配器。

# 介绍

对于那些不熟悉视图绑定的人来说，这是一个来自 Jetpack 家族的轻量级库，用于将视图与 Android 组件链接起来。在当前的 Android 环境中，我们有四种解决方案来链接视图和 Android 组件:

*   `findViewById` —这是涉及更多手动工作的传统解决方案。
*   Kotlin 人工合成——这是 Kotlin 本地库的一个不错的解决方案，但 Kotlin 团队最近否决了它。
*   数据绑定——这个库提供了比视图链接更高级的特性，比如双向绑定等等。
*   视图绑定—具有空值和类型安全代码的本机解决方案。

如果你有兴趣进一步了解这四种类型，我推荐你阅读[这篇文章](https://medium.com/better-programming/the-evolution-of-view-linking-in-android-d6219678740d)。

`ViewBinding` 背后的思想是为每一个布局创建一个绑定类，保存布局中视图的引用。这减少了组件(对话框和适配器)中的样板代码，我们可以通过创建该类的实例来访问视图。如果您是查看绑定的新手，请阅读下面的文章，了解如何添加该库并在高级 Android 组件(如活动和片段)中使用它。

[](https://medium.com/better-programming/everything-you-should-know-about-viewbinding-in-android-52552af9e8ba) [## 关于 Android 中的 ViewBinding 你应该知道的一切

### 没有 findViewById 了，该升级了。

medium.com](https://medium.com/better-programming/everything-you-should-know-about-viewbinding-in-android-52552af9e8ba) 

# 在对话框中查看绑定

一旦我们在 gradle 文件中启用了视图绑定，就会为项目中的每个布局创建一个绑定类。我们可以在任何 Android 组件中使用这个类来扩展布局。例如，让我们为一个对话框创建一个示例布局:

使用视图绑定的好处之一是，我们不需要像使用数据绑定时那样在 XML 文件中包含任何额外的布局。返回到对话框视图绑定，一旦创建了布局，就会生成该布局的新绑定类。在这种情况下，生成的文件是`DialogAlertCommonBinding`类。

下一步是将生成的文件链接到 Android 组件。在这种情况下，那就是`Dialog`。这里，我们需要使用`LayoutInflater`来扩展`DialogAlertCommonBinding`根视图:

就这么简单！不再有`findViewById`、视图转换、空指针异常或数据绑定“未知引用”问题。对话框不是主要的 Android 组件，比如`Activities`和片段，尽管视图绑定工作得完美而快速。

# 查看适配器中的绑定

正如我所说的，不管布局类型是什么(也就是说，即使布局被用在主要的 Android 组件中，如`Activity`或对话框等组件中)，一个单独的绑定类将会自动生成。适配器也是如此。让我们创建一个示例布局:

在`recyclerview`适配器中，我们需要在`createViewHolder`函数中展开视图，并在`bindViewHolder`函数中使用绑定实例。让我们从膨胀布局开始:

然后我们需要在`AppsListItemViewHolder`内部使用它:

然后我们需要用适配器位置项从`onBindViewHolder`调用`bind`函数。

# 结论

视图绑定的唯一目的是取代`findViewById`并提供一个类型安全和空安全的最小框架。与黄油刀和数据绑定等许多其他库不同，视图绑定提供了一个强大的轻量级解决方案。

正如我们在本文中看到的，即使在核心的内部组件如对话框和适配器上也很容易应用。我更喜欢使用视图绑定来提高效率，并创建一个长期受支持的安全代码库。

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [“Android 中的数据绑定”](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c)
*   [“安卓产品口味”](https://medium.com/@sgkantamani/android-product-flavors-eb526e35f9f1)
*   [“如何创建和发布 Android 库”](https://medium.com/@sgkantamani/how-to-create-and-publish-an-android-library-f37bf715932)

目前就这些。希望你学到了有用的东西。感谢阅读。