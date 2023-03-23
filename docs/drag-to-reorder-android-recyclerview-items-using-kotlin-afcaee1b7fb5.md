# 使用 Kotlin 拖移以重新排序 Android RecyclerView 项目

> 原文：<https://betterprogramming.pub/drag-to-reorder-android-recyclerview-items-using-kotlin-afcaee1b7fb5>

## 在你的 Android 应用中创建漂亮的可定制列表

![](img/463e0832c8856bfc204ed284faa33119.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在本文中，您将学习如何实现`RecyclerView`，它为您提供了重新排序项目的能力。我们将在不使用任何第三方库的情况下做到这一点。我们将使用`RecyclerView`默认装饰器和一些接口来实现这一点。如果您感兴趣，底部嵌入了一个带有实现的 GitHub repo 链接。

不要再拖延了，让我们开始吧！

# 介绍

在 Android 中显示巨大列表的最好方式之一是通过`RecyclerView`。如果你是一名 Android 开发者，你肯定用过它。我们有许多先进的功能，如视图持有人模式，丰富的动画，`DiffUtil.Callback`以提高性能，等等。像 WhatsApp 和 Gmail 这样的应用程序使用`RecyclerView`来显示无休止的对话。

许多开发人员觉得实现起来很困难的一个特性是重新排序列表项。在过去，我们必须手动完成所有事情，从检测触摸到更新列表项。但是随着 Android 开发的改进，我们已经有了一些方便的实用程序，比如`ItemTouchHelper`来帮助我们实现复杂的功能，比如在几个步骤中重新排序。

`ItemTouchHelper`不过是从`RecyclerView.ItemDecoration`扩展而来的简单类。这提供了多个回调，例如`onMove`、`onSwiped`、`onSelectedChanged`、`Clearview`等等。随着所有这些回调的及时使用，我们将在`RecyclerView`中实现重新排序。

# 实现 ItemTouchHelper

首先，我们需要创建一个 Kotlin 类并用`ItemTouchHelper.Callback`扩展它。然后我们覆盖`getMovementFlags`、`onMove`和`onSwiped`功能，如下所示:

`onSwipe`功能用于检测`RecyclerView`项目滑动，现在不需要。

现在，我们来看看另外两个函数:`getMovementFlags`和`onMove`。

`getMovementFlags`是一个功能，通过它我们可以注册运动标志*到*到`RecyclerView`项目。我们可以使用`makeMovementFlags`功能注册`swipe`和`drag`标志。看一看:

在这里，我已经注册了`swipe`和`drag`。如果不想包含`swipe`功能，那么将`0`作为第二个参数传递。我们的目标是在垂直的`RecyclerView`上实现一个重新排序的特性，所以我包含了`UP`和`DOWN` `drag`标志。使用哪一个将根据列表的方向而改变。

接下来是`onMove`功能。当`ItemTouchHelper`想要将拖动的项目从原来的位置移动到新的位置时执行。它有三个参数:

*   `@param recyclerView`—`ItemTouchHelper`所连接的`RecyclerView`。
*   `@param viewHolder` —用户正在拖动的`ViewHolder`。
*   `@param target` —`ViewHolder`当前活动项目被拖动到其上。

布尔型是函数的返回类型。如果你想让再订购发生，那么你需要通过`true`。如果需要，我们可以通过传递`false`将重新排序限制到特定的位置或视图类型。

我们有需要在适配器中更新的源和目标`ViewHolders`。现在我们需要在适配器和重新订购项目助手之间建立一个桥梁。为此，我们必须创建一个接口，并从适配器将它传递到 item helper 的构造函数中，以触发重新排序更新。看一下界面:

现在我们需要将这个接口作为构造函数中的一个参数，如下所示:

```
class ReorderHelperCallback(**val adapter : ItemTouchHelperAdapter**) 
    : ItemTouchHelper.Callback() {
```

我们将使用`onMove`函数中的`adapter`接口与实际的`recyclerView`适配器进行通信:

至此，我们已经基本完成了核心逻辑。默认情况下，长按`RecyclerView`项将触发重新订购。但是如果您希望提供一个重新排序按钮来方便用户呢？我们能做到！

我们需要创建另一个接口，并将其命名为`OnStartDragListener`。当一个视图被请求开始拖动时，它只有一个执行的函数。看一看:

这个接口在`ViewHolder`中用来触发移动，这将在本文的下一节中看到。

# 再循环视图固定器

首先，我们需要实现一个通用的`RecyclerView` holder。这里，我将使用`ViewBinding`来引用布局中的视图。如果你是这方面的新手，请在继续之前阅读本文。

看看一个简单的`ViewHolder`和`ViewBinding`以及一个数据类:

这很好，但是如果您想从一个特定的视图中触发重新订购，我们需要从`ViewHolder`中完成。为了调用`drag`，我们创建了`OnStartDragListener`接口。这里，我们需要通过一个`ViewHolder`构造函数传递它的实例，并在该视图的触摸监听器内部调用`onStartDrag`:

# RecyclerView 适配器

同样，我们需要实现一个简单的具有必要功能的`RecyclerView`适配器(例如`onCreateViewHolder`、`onBindViewHolder`等等)。看一看:

现在我们已经完成了基本功能，是时候实现重新排序功能了。我们已经创建了`ItemTouchHelperAdapter`接口来监听一个项目何时被拖动足够远来触发移动。这将覆盖`onItemMove`函数，其中我们将交换数组项并使用`notifyItemMoved`函数更新 UI。看一看:

# 实施重新排序

最后，我们完成了`ItemTouchHelper`、适配器、`ViewHolder`和接口。是时候给`RecyclerView`分配适配器了。为了创建重排序适配器的实例，我们需要`OnStartDragListener`实例。我们只需要用`OnStartDragListener`扩展 Android 组件(在本例中是`activity`)并传递`activity`上下文。看一看:

接下来，我们需要创建一个`ReorderHelperCallback`实例，并将其用作`ItemTouchHelper`的回调，我们将把它附加到实际的`RecyclerView`:

仅此而已！你已经成功地实现了`RecyclerView`在没有任何第三方库的情况下重新排序项目！

# 奖金

要了解有关 Android 中的`RecyclerView`适配器的更多信息，请阅读以下文章:

*   [在 Kotlin 中构建反应式&异构适配器](https://medium.com/android-dev-hacks/building-a-reactive-heterogeneous-adapter-in-kotlin-eed9487df29b)
*   [Android 中适配器的演变](https://medium.com/better-programming/evolution-of-adapters-in-android-2e2ff58c0f98)
*   [如何在 Android 中顺序合并适配器](https://medium.com/better-programming/how-to-merge-adapters-sequentially-in-android-11914eac53d)
*   [Android recycler 查看 Kotlin 密封类](https://medium.com/better-programming/android-recyclerview-with-kotlin-sealed-classes-6d2985aac3e5)

# GitHub 回购

[](https://github.com/SG-K/RecyclerviewWithBenefits#drag-and-reorder-recyclerview-items) [## SG-K/recyclerviewithsbenefits

### 这是一个演示应用程序，旨在为开发人员展示如何实现复杂的功能，如重新排序、滑动等…

github.com](https://github.com/SG-K/RecyclerviewWithBenefits#drag-and-reorder-recyclerview-items) 

目前就这些。希望你学到了有用的东西。感谢阅读！