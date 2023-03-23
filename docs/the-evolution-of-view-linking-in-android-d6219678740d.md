# Android 中视图链接的演变

> 原文：<https://betterprogramming.pub/the-evolution-of-view-linking-in-android-d6219678740d>

## findViewById >黄油刀>数据绑定> Kotlin 合成>视图绑定

![](img/decd8374df1d9ca8cafbd0027e0fed71.png)

丹尼尔·利维斯·佩鲁西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Android 开发环境从一开始就基于三种类型的文件:XML、Kotlin 和 Java。XML 文件包含与设计相关的一切，而 Kotlin/Java 文件包含除设计部分之外的任何内容。

最终，UI 和业务逻辑需要链接起来，这就是本文所要讨论的。让我们看看在布局中查找视图是如何演变的。

# ' findViewByID '

`findViewByID`是该系列中的第一个，由 Android 团队在 API Level 1 中引入。这个函数提供了一个视图对象作为返回类型，开发人员将这个对象链接到在`View`类中创建的视图。

Android 中的每个视图都是从`View`类扩展而来的。因此，XML 布局中使用的所有视图都以某种方式扩展到了`View`类。`findViewByID`返回该类的一个实例。

“findViewByID”的用法

最近，Android 团队对该功能进行了增强，以克服视图投射。因此，开发人员不再需要在与`findViewByID`链接时转换视图。

尽管努力增强`findViewByID`，许多开发者觉得这不是最好的方法。所以人们开始创造像黄油刀这样的替代品，在某个时候，Android 团队提出了数据绑定(尽管它的主要焦点不仅仅是链接视图)。

# 黄油刀

Android Butter Knife 库是一个轻量级的 view0injection 库，用于注释。这是第一个似乎是传统的`findViewByID`的成功替代品的图书馆。

它是由杰克·沃顿(Jake Wharton)创建的，你可以在其官方[网站](http://jakewharton.github.io/butterknife/)上找到如何使用黄油刀的文档。该库使用`@BindView`注释链接布局中的视图。有一段时间，开发人员将它作为`findViewByID`的正式替代品。让我们来看看如何使用它:

黄油刀的用法

和`findViewByID`差不多；对于开发人员来说，删除样板代码的唯一好处是我们不需要单独实现视图的声明和初始化。

# 数据绑定

[数据绑定库](https://developer.android.com/topic/libraries/data-binding)是一个支持库，允许您使用声明性格式将布局中的 UI 组件绑定到应用程序中的数据源，而不是以编程方式。

数据绑定库为布局生成绑定类，我们可以在像`Activity`和`Fragment`这样的 Android 组件中使用它们。数据绑定是一种具有类型和空安全的声明性解决方案。

为了使布局支持数据绑定，我们必须用`layout`标签包装文件的内容。这将生成该布局的绑定文件，并引用其中的所有视图。看一看:

将根内容包装在“layout”标记中

一旦我们完成了 XML 中的这一部分，就会生成一个绑定类，我们需要在`View`类中使用它。这个绑定文件实例包含布局中的所有视图。看一看:

“View”类中的数据绑定

这似乎是一个合理的解决方案，但是创建数据绑定是为了解决比链接视图更复杂的问题，比如访问`Data`类的布局文件从 XML 本身发布数据，以及使用绑定适配器加载远程图像。

这些事情使得数据绑定成为解决视图链接问题的复杂库。本质上，数据绑定是为了解决复杂的问题而创建的，因为它会增加编译时间，而且错误也很难理解。

# 科特林合成纤维

当 Kotlin 被引入 Android 开发时，它解决了许多问题，包括视图链接。Kotlin Extensions 是 Kotlin 团队开发的一个插件。它消除了在代码中使用`findViewById`的需要。

在 Kotlin 扩展的帮助下，合成代码在 Android 中应运而生，不使用`findViewByID`就解决了 Android 中的视图链接问题。与这里的其他库不同，我们可以在类文件中直接使用视图的 ID。

Kotlin synthetics 第一次调用`findViewById`函数，然后在默认情况下将视图实例缓存在`HashMap`中。该缓存配置可通过梯度设置更改为`SparseArray`或无缓存。

Kotlin synthetics 是最好的方法之一——它是类型安全的，我们可以直接从布局中使用 id。通过使用 Kotlin 的`?`操作符，我们可以使这个过程不安全。

除了好处之外，我们还有一个问题——如果我们使用视图的 ID，Android Studio 会显示自动完成，即使膨胀的布局与您试图访问的视图不同。在这种情况下，如果您不使用`?`操作符，您的应用程序将会崩溃。

# 视图绑定

最后`ViewBinding`，是一个链接视图到类文件的官方解决方案。`ViewBinding`背后的想法是为每个布局创建一个绑定类，保存布局中视图的引用。这减少了组件中的样板代码(`Activity`和`Fragment`)，我们可以通过创建该类的实例来访问视图。

有了视图绑定，就像在数据绑定中一样，在对视图执行操作时，不再需要对视图进行空检查。作为直接从布局文件生成的绑定类，它只包含布局内的视图。

类型安全是视图绑定最显著的优点之一。如果你试图给一个按钮分配一个文本监视器，它不会接受，因为`Binding`类包含了适当视图类型的引用。

视图绑定的唯一目的是取代`findviewbyId`的使用。与数据绑定不同，它是轻量级的，所以编译不会花太多时间。

查看绑定用法示例代码

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [“如何将 Google Pay 集成到您现有的 Android 应用中”](https://medium.com/better-programming/how-to-integrate-google-pay-into-your-existing-android-app-d75b269cd623)
*   [《安卓产品口味》](https://medium.com/@sgkantamani/android-product-flavors-eb526e35f9f1)
*   [“如何创建和发布 Android 库”](https://medium.com/@sgkantamani/how-to-create-and-publish-an-android-library-f37bf715932)
*   [“Android 中适配器的演变”](https://medium.com/better-programming/evolution-of-adapters-in-android-2e2ff58c0f98)

目前就这些——希望你学到了有用的东西。感谢阅读。