# 面向 Android 开发者的 5 款 Kotlin 扩展

> 原文：<https://betterprogramming.pub/5-more-kotlin-extensions-for-android-developers-3857b1f16407>

## 联机状态、权限、可扩展字符串等

![](img/a0efd07fdb1374b4135728e78a934029.png)

照片由 [Ave Calvar](https://unsplash.com/@shotbyrain?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

您可能已经阅读了大量与 Kotlin 扩展相关的文章，但本文并不完全是关于扩展的。这是为了让你的代码更有表现力，所以我列出了我最喜欢的扩展，让代码尽可能自然。

这是本系列的第二部分。如果你是 Kotlin 或扩展概念的新手，我强烈推荐你阅读第一部分来学习基础知识。

# 1.isOnline

在大多数移动应用程序中，网络请求是不可避免的任务。检查用户是否连接到互联网至关重要。更进一步，一些应用程序在用户界面的顶部或底部显示一个小的 snackbar 类型的横幅，以指示用户在线/离线。

检查用户是否连接到互联网几乎是任何移动开发者的日常任务。因此，我们实现的代码应该是整洁的，并且在没有任何先验知识的情况下表达自己。

我使用 Kotlin 扩展主要是为了这些用例。大多数情况下，我们试图在我们的片段、活动、服务等中检查互联网连接。其中常见的一个就是 Android `Context`。我们可以在名为`isOnline`的`Context`类上创建一个扩展函数并实现代码，如下所示:

如果用户在线，该函数返回`true`。否则，它返回`false`。我们还可以在 Kotlin 中使用 lambdas 和高阶函数，使其更具声明性。看一看:

这里，我们移除了布尔返回类型，并使用高阶函数来传递应该在各自的事件中执行的函数。所以现在我们不需要在调用点使用`if`语句。相反，我们使用 lambda 函数:

# 2.显示或隐藏键盘

安卓键盘事件维护的一塌糊涂。最近，Android 团队一直在关注这个问题，并提供了类似 window insets 等解决方案。坦率地说，大多数开发人员还没有采用它们。在我的日常工作中，我使用以下扩展函数来显示和关闭键盘。

在 Kotlin 之前，我将这些函数用作`Utility`类静态函数，并从单例实例中调用它们，这是一种更古怪的方法。然后我决定让它尽可能自然地触发，所以我创建了两个视图扩展函数来打开和关闭键盘:

# 3.权限检查

这是我最喜欢的扩展之一，因为它提高了我的生产率，同时增强了代码的可读性。谷歌为 Android 6 引入了动态权限。这是一个很好的安全方法，但是实现部分包含了大量的样板代码。

有几个很棒的库可以让你的日常开发变得简单，比如 [Dexter](https://github.com/Karumi/Dexter) 、 [PermissionsDispatcher](https://github.com/permissions-dispatcher/PermissionsDispatcher) 和 [EazyPermissions](https://github.com/sagar-viradiya/eazypermissions) 。但是这些解决方案通常会在代码库中添加更多不必要的代码。

作为所有这些方法的简单替代，我们可以创建一些扩展函数来简化它:

*注:我改编了威廉·古韦亚的文章* *中的* [*的这些扩展。*](https://proandroiddev.com/easy-way-to-ask-for-permissions-on-android-62a9ae4a22b0)

# 4.可延展的字符串

无论您在哪个平台上工作，有时您会想要突出显示字符串的某个部分以获得用户焦点，或者您想要为字符串的某个特定部分分配一个点击监听器。这可以在 Android 环境中使用 spannable 字符串来实现。

下面是我用来简化可扩展字符串代码的一些扩展:

使用这些扩展，我们可以在调用点编写整洁的代码。

# 5.颜色转换扩展

在 Android 中，我们通常用十六进制格式声明颜色。但我们将遇到的不仅仅是这种形式。一些流行的库使用 RGB 和整数，所以我们可以快速转换成任何颜色格式是很重要的。为此，我使用了以下扩展:

当我处理调色板时，这两个扩展使我的生活变得简单。当然，您可以根据需要创建自己的扩展。例如，如果你需要的话，你可以创建 RGB 转十六进制。

# 奖金

要了解更多关于协同程序和 Kotlin 的其他高级特性，请阅读以下文章:

*   [“5 个 Kotlin 扩展让你的 Android 代码更有表现力”](/5-kotlin-extensions-to-make-your-android-code-more-expressive-4c9243cb9466)
*   [“KTX 库的 5 个强大的 Kotlin 扩展”](/5-powerful-kotlin-extensions-from-ktx-libraries-b10c50504df6)
*   [“用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)
*   [“科特林协程，从基础到高级](https://medium.com/better-programming/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)”
*   [“使用新的 Kotlin 流进行异步数据加载](https://medium.com/better-programming/asynchronous-data-loading-with-new-kotlin-flow-233f85ae1d8b)”
*   [“为什么以及如何使用 Kotlin 的原生序列化库](https://medium.com/better-programming/why-and-how-to-use-kotlins-native-serialization-library-c88c0f14f93d)”

目前就这些。希望你学到了有用的东西。感谢阅读。