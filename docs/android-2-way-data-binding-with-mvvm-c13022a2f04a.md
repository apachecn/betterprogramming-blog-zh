# Android 与 MVVM 的双向数据绑定

> 原文：<https://betterprogramming.pub/android-2-way-data-binding-with-mvvm-c13022a2f04a>

## 将数据绑定的力量与架构组件结合起来

![](img/c98341f5ee76ad176f1a42d0faae19f0.png)

照片由 [Marissa & Eric](https://unsplash.com/@emtm17?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

这是一个系列的开始，在这个系列中，您将学习数据绑定基础知识，以及如何将数据绑定用于像`LiveData`和`ViewModel`这样的架构组件。

在这一部分中，我们将介绍基础知识、双向绑定和代码优化。

# 介绍

数据绑定是一个框架，它通过减少开发人员需要编写的样板代码来充当布局和数据源之间的桥梁。这将为他们提供时间专注于重要的事情，从而提高他们的工作效率。

我假设您熟悉数据绑定的基础知识。如果你不是，我强烈建议你在继续之前阅读下面的文章:

[](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c) [## Android 中的数据绑定

### 不再需要编程来更新 UI 并将事件链接到视图

sgkantamani.medium.com](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c) 

现在我们都知道什么是数据绑定了，那么什么是双向数据绑定呢？

答案很简单。从数据源更新视图是一个简单的单向绑定。在这种情况下，您将只访问数据源中的数据并更新布局。

双向数据绑定只不过是在布局发生变化时更新数据源，反之亦然。双向数据绑定并不适用于 Android 中的所有视图。例如，在`EditText`中使用双向数据绑定是有意义的，因为用户可以更新视图中的数据。但是不建议对`TextView`做同样的事情，因为用户不能改变`TextView`中的数据。

# 履行

让我们从单向数据绑定开始。我们将创建一个简单的 Kotlin 模型类，并使用数据绑定从布局中访问它。首先，让我们创建一个`UserModel`类:

```
class **UserModel**(var **name** : String, var **email** : String )
```

接下来，我们需要从布局文件中访问这个模型，并将这些变量分配给视图。因为我们从单向数据绑定开始，所以我们使用`@{}`将数据源绑定到视图:

现在，每当数据源中的初始值更新时，布局也会更新。但是这不足以反映数据源每次更新的 UI。

如果模型中有任何变化，为了更新 UI，我们需要使模型成为一个可观察的组件。使其可观察的第一步是从数据绑定框架用`BaseObservable`扩展模型类:

```
class UserModel () : **BaseObservable()**{

    var name : String = ""
    var email : String = ""

}
```

然后我们需要注释 UI 中的每个变量，用`[@get](http://twitter.com/get):Bindable`动态更新，并调用`set`委托上的`notifyPropertyChanged`来更新 UI:

现在，UI 会动态刷新。

我们现在完成了单向可观察数据绑定。下一步是实现简单的双向数据绑定。让我们后退一步，评估一下形势。

目前，如果数据源有任何变化，数据绑定将负责刷新用户界面。我们需要实现的唯一一件事是，如果 UI 中有任何变化，不管数据源是什么，都要更新数据源。对于这一点，数据绑定有一个现成的简单解决方案。我们只需要在布局中用`@={}`更新`@{}`，剩下的就交给框架处理了。

在我们的例子中，我们将使用`name`进行双向数据绑定，因为它是一个用户可以更新的`EditText`值:

仅此而已！我们已经成功实现了双向数据绑定。

# 最佳化

上面的实现工作起来没有任何问题，但是如果您仔细观察，就会发现涉及到太多的手工工作。默认情况下，数据绑定为主数据类型提供了`ObservableField`。比如我们可以用`ObservableField<String>`代替`String`来表示用户模型中的`name`和`email`。

因此，在幕后，数据绑定会处理样板代码，这样我们就不需要一直写了。看看优化后的模型类:

需要记住的一点是，移除`BaseObservable`会使 `UserModel`成为一个不可观察的物体。但是通过使其变量`ObservableFields`，双向绑定不会受到影响。使用哪种结构完全取决于您。

# 监听器和实时用例

数据绑定是一个强大的框架，具有强大的功能，有助于减少开发人员每天编写的大量样板代码。

数据绑定提供的不仅仅是`ObservableFields`。我们可以实现点击监听器，利用`Elvis`操作符为布局中的属性分配适当的值，创建定制适配器来加载图像或应用定制字体，等等。

从长远来看，学习如何在 XML 中实现所有这些类型的特性将会提高开发人员的生产力。这些是基础，所以如果你是数据绑定的新手，我强烈推荐[阅读这篇文章](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c)。

# 下一部分会发生什么

既然我们已经学习了双向数据绑定和如何优化代码，在下一篇文章中，我们将学习如何使用双向绑定和类似于`LiveData`和`ViewModel`的架构组件。敬请期待！

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [《Android 开发中的 8 个常见错误》](https://medium.com/better-programming/8-common-mistakes-in-android-development-2edcf5179ec0)
*   [“关于 Android 中 ViewBinding 你应该知道的一切”](https://medium.com/better-programming/everything-you-should-know-about-viewbinding-in-android-52552af9e8ba)
*   [“探索 kot Lin DSL”](https://medium.com/better-programming/exploring-kotlin-dsl-4ab67ed13062)
*   [“Android 中视图链接的演变”](https://medium.com/better-programming/the-evolution-of-view-linking-in-android-d6219678740d)

目前就这些。希望你学到了有用的东西。感谢阅读。