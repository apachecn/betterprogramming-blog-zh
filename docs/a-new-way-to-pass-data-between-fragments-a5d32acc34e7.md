# 一种在片段之间传递数据的新方法

> 原文：<https://betterprogramming.pub/a-new-way-to-pass-data-between-fragments-a5d32acc34e7>

## 在 Android 片段中使用新的**result listener API**

![](img/babc560cf727b2d5782acdd4864db310.png)

由 [Alexandru Acea](https://unsplash.com/@alexacea?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

自从 Android 架构组件出现以来，Android 开发变得更加简单和高效。另一方面，像真理的单一来源和单一活动这样的概念带来了巨大的转变。

有了这样高质量的开发思路，开发者往往会从传统的活动转向有效的片段。这使得许多开发人员(在某些情况下包括我自己)在片段之间的数据通信方面处于一个棘手的位置。

# 问题

在没有任何引用(上下文、接口、共享视图模型或应用程序级实时数据)的情况下，将数据从一个片段传递到另一个片段似乎几乎是不可能的。但是如果你看得更深入一点，你会找到解决办法:`targetFragment`。

`targetFragment`提供了一种与后台堆栈中的片段进行通信的方式。不需要在源中引用任何目标。简单来说，`targetFragment`是一种分段实现`onActivityResult`功能的方式。

## 它是如何工作的

让我们在父片段中调用`showOptionsDialog()`，它显示了一个带有选项列表的`OptionsDialogFragment`。我们在这里做的唯一新的事情是用请求代码设置目标片段:

然后我们需要在需要接收数据的片段中实现`onActivityResult`。之后，剩下的唯一事情就是传递来自源片段的数据。看一看:

嗯，这很好。我们可以将数据传递给一个片段，而不引用它。但是问题是`targetFragment`的功能只有当源和目的片段都在同一个片段管理器上时才起作用。如果一个片段在`childfragmentmanager`上膨胀，那么`targetFragment`就不起作用了。

# 解决办法

近十年后，Android 团队开始关注这个问题。随着`[Fragment 1.3.0-alpha04](https://developer.android.com/jetpack/androidx/releases/fragment#1.3.0-alpha04)`的发布，现在每个`[FragmentManager](https://developer.android.com/reference/androidx/fragment/app/FragmentManager)`都实现了`[FragmentResultOwner](https://developer.android.com/reference/androidx/fragment/app/FragmentResultOwner)`。这意味着`FragmentManager`可以作为片段结果的中央存储。

这种改变允许各个片段通过设置片段结果并监听这些结果来相互通信，而不需要片段具有彼此的直接引用。不像`targetFragment`，它跨片段管理器工作。

## 它是如何工作的

首先，让我们看看如何在`FragmentManager`级别传递数据，然后在父节点和子节点`FragmentManagers`之间传递数据。为了将数据从源片段传递到目的片段，我们必须添加一个带有特定键的监听器。这里只调用由任何其他片段发布的带有这个键的包。看一看:

我们需要检索数据的源片段中的侦听器

现在，是时候传递来自源片段的数据了，如下所示:

使用 setResult 从源片段传递数据

当两个片段都在同一个`FragmentManager`上时，这非常简单并且工作良好。`setResult`如果多次发布，总是将最新数据发送到目的地。如果在调用`setResult`时没有设置监听器，它将在指定监听器时存储数据和交付。最重要的是，记住您应该只声明一个带有特定键的侦听器。

子片段和父片段之间的交流呢？嗯，我们也有解决办法。从高层次来解释，子片段将数据传递给`childfragmentmanager`，然后它将数据传递给父片段。实现部分和上面类似。唯一的区别是我们需要将监听器添加到子片段管理器中，如下所示:

在子片段中设置侦听器

仅此而已。传递数据同上。我们需要用一个惟一的键和绑定的数据来调用`setResult`。

# 结论

我知道我们都习惯于使用一个界面，共享的视图模型，以及更多的片段间交流的方式。但是取消`targetfragment`并引入片段结果 API 提供了一个平台原生的解决方案。它仍处于 alpha 阶段，我认为它在未来的日子里会非常有用，因为我们比以往任何时候都更关注面向片段的开发。

希望你学到了有用的东西。感谢阅读。