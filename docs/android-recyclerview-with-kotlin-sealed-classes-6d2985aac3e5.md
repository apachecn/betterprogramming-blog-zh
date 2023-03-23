# 使用 Kotlin 密封类的 Android RecyclerView

> 原文：<https://betterprogramming.pub/android-recyclerview-with-kotlin-sealed-classes-6d2985aac3e5>

## 使用密封类的 RecyclerViews 中的多种视图类型

![](img/7a6737d63f75c3b3eca3c86cd6fec1d6.png)

Stephen Frank 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在 Android 中显示巨大列表的最好方式之一是通过`RecyclerView`。作为开发人员，你们可能都用过它。我们有许多高级特性，如视图保持器模式、丰富的动画、提高性能的差异实用程序回调等。像 WhatsApp 和 Gmail 这样的应用程序使用`RecyclerView`来显示无休止的对话。

我使用的一个重要的`RecyclerView`特性是视图类型。我们可以在同一个`RecyclerView`中显示多个视图类型。在早期，开发人员通过在列表模型中维护一个视图类型标志并在`RecyclerView`适配器的`getViewType`函数中返回它来实现这一点。

# 科特林为什么封课？

在为 Android 开发引入 Kotlin 之后，我们处理核心实现的方式发生了巨大的变化。我的意思是，像扩展这样的特性几乎取代了维护 Android 组件基类的需要。Kotlin 代理改变了我们使用 setters 和 getters 的方式。

现在，是时候更新我们在`RecyclerView`适配器中的工作方式了。Kotlin 密封类显示出对状态管理的显著影响。如果你想了解他们，我建议你阅读这篇文章。

受那篇文章的启发，我想使用密封类在`RecyclerView`中实现视图类型。我们将尝试将随机整数或布局的比较转移到类类型。如果你是一个 Kotlin 书呆子，我相信你会喜欢这个实现。

# 创建 Kotlin 密封类

在这种方法中，我们需要做的第一件事是创建我们希望在适配器中使用的所有数据类，然后将它们链接到一个密封的类中。让我们创建一组数据类:

这是我希望在基于服务器数据的列表中显示的几个数据类。您可以拥有任意多的数据类。这种方法可以大规模使用。

它最好的一点是，我们可以维护加载状态、页眉、页脚等等，而无需编写任何额外的类。一会儿你就知道怎么做了。下一步是创建保存所有必要数据类的密封类:

带有自定义模型的密封类

正如我之前说过的，我们可以通过使用 Kotlin 中的对象在`RecyclerView`中添加页眉和页脚，而不需要任何额外的工作:

带有页眉和页脚的密封类

现在，我们完成了密封类的实现。

# 创建 RecyclerView 适配器

现在我们已经完成了一个密封类，是时候用`UIModel`列表创建一个`RecyclerView`适配器了。这是一个简单的`RecyclerView`，但是有一个密封的类`arraylist`:

上面的代码显示了一个没有任何密封类逻辑的`RecyclerView`适配器的基本实现。如你所见，我们声明了一个密封的类 arraylist ( `UIModel`)。下一步是根据位置返回适当的视图类型:

比较密封的类模型以获取视图类型

既然我们已经成功地返回了基于密封类模型的正确布局，我们需要基于`viewtype`在`onCreateViewHolder`函数中创建各自的`ViewHolder`:

从密封类创建与视图类型相关的视图容器

最后一部分是用当前项目数据更新视图容器，以便适配器可以在 UI 中反映数据。由于适配器有多个视图，我们必须对类型进行分类，然后调用各自的`ViewHolder`:

当我们将这些片段组合在一起时，最终的代码如下所示:

最终适配器

在这一点上，我们可以走了。我们已经实施了所有强制性的东西。您可以在您的`Activity/Fragment`中创建一个适配器实例，并将其分配给`RecyclerView`。一旦获得数据，您需要用`ArrayList<UIModel>`调用`submitData`函数:

将数据发布到 RecyclerView 适配器

# **DiffCallback**

> " DiffUtil 是一个实用程序类，它可以计算两个列表之间的差异，并输出一个更新操作列表，将第一个列表转换为第二个列表。"— [安卓开发者](https://developer.android.com/reference/androidx/recyclerview/widget/DiffUtil)

实现`diffcallback`不是强制性的，但是如果你正在处理大型数据集，它会提高性能。因此，要在我们的适配器中实现`difCallback`，我们需要区分模型并比较必要的变量:

difCallback 实现

这类似于常规的`diffcallback`实现，但是我们需要分离类型。一旦创建了它，就在构造函数中将它链接到适配器。

目前就这些。希望你学到了有用的东西。感谢阅读。