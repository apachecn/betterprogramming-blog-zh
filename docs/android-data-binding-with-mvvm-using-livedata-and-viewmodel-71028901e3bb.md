# 与 MVVM 的 Android 数据绑定:使用 LiveData 和 ViewModel

> 原文：<https://betterprogramming.pub/android-data-binding-with-mvvm-using-livedata-and-viewmodel-71028901e3bb>

## 构建基于 MVVM 架构的应用程序指南

![](img/16a46a57c421d16f84fbbc6877b61ff9.png)

照片由[威廉·艾文](https://unsplash.com/@firmbee?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

这是系列文章的第二部分[，在这个系列文章中，您将学习数据绑定基础知识，以及如何将数据绑定用于像`LiveData`和`ViewModel`这样的架构组件。在今天的文章中，您将学习如何使用`LiveData`通过数据绑定更新 UI，将`ViewModel`作为数据源。](/android-2-way-data-binding-with-mvvm-c13022a2f04a)

# 介绍

## 数据绑定

数据绑定是一个框架，它通过减少开发人员需要编写的样板代码来充当布局和数据源之间的桥梁。这将提高开发人员的生产力，让他们有时间专注于重要的事情。

如果您是数据绑定的新手，我建议您阅读下面的文章和本系列的第一部分。今天的文章是关于如何在架构中使用数据绑定。要理解这一点，你应该做好基础工作。

[](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c) [## Android 中的数据绑定

### 不再需要编程来更新 UI 并将事件链接到视图

sgkantamani.medium.com](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c) 

## LiveData

简单来说，`LiveData`是一个可观测的数据持有者类。这意味着它可以保存一组可以从其他 Android 组件(如`Activities`、`Fragments`和`Services`)中观察到的数据。它是生命周期感知的，主要用于更新 MVVM 建筑项目中`ViewModel`的 UI。要了解更多关于`LiveData`的信息，请阅读以下文章:

[](https://medium.com/android-dev-hacks/exploring-livedata-and-kotlin-flow-7c8d8e706324) [## 探索 LiveData 和 Kotlin 流

### 何时何地使用 LiveData 和 Kotlin Flow

medium.com](https://medium.com/android-dev-hacks/exploring-livedata-and-kotlin-flow-7c8d8e706324) 

## 视图模型

`ViewModel`是一个架构组件，用于使用 MVVM 实现 Android 应用。组件`ViewModel`在 Android 开发中至关重要，因为它是生命周期感知的，并用于实现*关注点分离*原则。要了解更多信息，请阅读以下文章:

[](https://medium.com/better-programming/best-practices-for-using-viewmodels-in-android-e986b25dc78f) [## 在 Android 中使用视图模型的最佳实践

### 如何正确设置和使用 Java 和 Kotlin 视图模型

medium.com](https://medium.com/better-programming/best-practices-for-using-viewmodels-in-android-e986b25dc78f) 

# 履行

让我们继续本系列前一部分中使用的例子。在第 1 部分中，我们直接将`Activity`中的`UserModel`作为数据源导入绑定。在这一部分，我们将创建一个`ViewModel`，并从`ViewModel`实例中访问`UserModel`。

首先，让我们创建一个`ViewModel`并在其中包含一个`UserModel`实例作为变量。看一看:

```
class **UserViewModel** : ViewModel() {

    **var userModel : UserModel = UserModel()**

}
```

接下来，我们需要在布局中将数据源从`UserModel`更改为`ViewModel`。然后访问`ViewModel`中的`usermodel`实例:

此时，代码工作正常。就可以毫无疑问的推向生产了吧？

或者有什么我们可以做得更好的吗？

答案是肯定的——我们可以做得更好。还记得我们在本系列第一部分中用来优化代码的`ObservableFields`吗？让我们来看看:

每次创建一个数据类的工作量非常大。Kotlin 数据类应该让我们的生活变得更好，所以不要把它们复杂化。我们只是将变量的数据类型改为`String`而不是`ObservableField<String>`:

```
data class UserModel (var name: **String** ="" ,
                 var email: **String** = "")
```

通过将数据类型从`ObservableField<String>`更改为`String`，我们消除了变量的可见性。为了恢复能力，我们将使用`LiveData`。

我们需要将`usermodel`变量转换成`UserModel`类型的`LiveData`，如下所示:

```
class UserViewModel : ViewModel() {

    var userModel  = **MutableLiveData<UserModel>()**

}
```

仅此而已。您不需要在布局文件中做任何事情。一切都将由幕后的数据绑定框架来处理。现在有了`LiveData`功能，当应用程序在后台运行时，用户界面更新将是生命周期感知的，无需使用系统资源。

# 将数据分配给绑定

现在我们需要根据您的选择创建一个`Activity` / `Fragment`，然后通过生成的数据绑定布局类来扩展布局。看一看:

接下来，我们需要创建一个`ViewModel`的实例，并将其添加到绑定变量，这样`ViewModel`中的数据将绑定到布局:

现在你有了一个使用`ViewModel`、`LiveData`和数据绑定的应用程序。只要看看遵循这种模式，您需要编写的样板代码会减少多少。活动中没有`LiveData`可观察项，我们不再需要手动给视图赋值，也不需要编写点击监听器，等等。

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [“Android recycler view With kot Lin 密封类”](https://medium.com/better-programming/android-recyclerview-with-kotlin-sealed-classes-6d2985aac3e5)
*   [“使用 Android 构建变体管理服务器环境”](https://medium.com/better-programming/use-android-build-variants-to-manage-server-environments-146125ae31d2)
*   [“使用 BuildSrc 和 Kotlin DSL 进行梯度依赖管理”](https://medium.com/better-programming/gradle-dependency-management-with-buildsrc-and-kotlin-dsl-1de958eab166)
*   [“Android 中视图链接的演变”](https://medium.com/better-programming/the-evolution-of-view-linking-in-android-d6219678740d)

目前就这些。希望你学到了有用的东西。感谢阅读。