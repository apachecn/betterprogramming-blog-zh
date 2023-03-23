# 如何在你的 Android 库中使用 Dagger2

> 原文：<https://betterprogramming.pub/how-to-use-dagger2-in-your-android-library-bc6741dfbba4>

## 使用 Dagger2 在 Android 库中实现依赖注入

![](img/274bba9317cae1924267eb621867809f.png)

吉米·张在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 从这篇文章中吸取教训

在本文中，您将学习如何使用 Dagger2 在 Android 库中实现依赖注入。本文的重点是 AAR 库格式，但是我们在这里遵循的过程可以应用于任何库格式。

# 介绍

依赖注入是类获取其他类的引用的另一种方式。例如，有一个类`BananaMilkshake`，它可能需要`Milk`类。在这里，`BananaMilkShake`依赖于`Milk`类。通常这些必需的类，像`Milk`，被称为依赖。

实现依赖注入为您提供了以下优势:

*   代码的可重用性
*   易于重构
*   易于测试

依赖注入有三种类型:

1.  在类本身中创建所需的对象(类似于在`BananaMilkshake`类中创建的`Milk`类对象)。
2.  从其他地方获取所需的对象(比如 Android-activity 组件中的上下文)。
3.  提供所需的类对象作为参数(可以通过构造函数来创建)。

# 先决条件

在学习如何在 Android 库中实现 DI 之前，学习如何使用 Dagger2 在 Android 项目中实现 DI 是非常重要的。如果你是这个概念的新手，我强烈推荐阅读以下几篇文章。

*   [“使用 Dagger2 在 Android 中进行依赖注入”](/dependency-injection-in-android-with-dagger2-d260b8a72bb0)
*   [“如何在 Android 中用 Dagger2 建立依赖注入框架”](/injecting-android-viewmodels-with-dagger2-in-clean-architecture-744c1fe81530)
*   [“在干净的架构中使用 Dagger2 注入 Android 视图模型”](/injecting-android-viewmodels-with-dagger2-in-clean-architecture-744c1fe81530)

# Android 库

让我们来谈谈 Android 库，通常开发人员在拥有一个正在多个项目中使用的特性时会创建库。通过将该功能开发为 Android 库，它可以在组织的应用程序中重用，如果需要，他们可以外包该库。

出于安全考虑，许多开发人员选择使用 Android Archive(AAR)格式库来构建。我完全同意他们的观点；AAR 很容易在 maven 存储库中构建和托管，它提供了来自宿主应用程序的代码的完全抽象。如果你想了解更多关于 AAR 的信息，请阅读下面的文章:

[](https://sgkantamani.medium.com/how-to-create-an-android-archive-aar-library-f940cbf5280) [## 如何创建 Android 归档(AAR)库

### 你应该知道的关于 AAR 的一切。

sgkantamani.medium.com](https://sgkantamani.medium.com/how-to-create-an-android-archive-aar-library-f940cbf5280) 

# Android 库中的 Dagger2

最后来看本文的主要部分，开发人员通常创建应用程序范围组件，并将所有模块链接到它们。最后，它将从 Android 应用程序类中触发。

但是对于 Android 库来说，在库中有一个应用程序类并不是最佳实践，因为主机可能已经有了一个应用程序类。在库中创建另一个应用程序类将会产生冲突。

需要记住的另一个方面是，主机应用程序可能会使用 Dagger2、Hilt 或它自己的依赖注入逻辑。因此，我们不应该依赖主机应用程序来处理与 DI 相关的任何事情。

这里的解决方案是库 dagger 对象图不应该依赖于宿主应用程序。幸运的是，Dagger 能够从应用程序、活动或片段中初始化对象图。所以在我们的例子中，我们可以使用 activity 或者 Fragment，尽管我建议从 Activity 开始。

# Android 库中的前期工作

为了更好地理解这种方法，让我们创建一个简单的框架，其中有一个存储库接口及其实现，以及一个简单的数据提供者类。让我们从具有返回字符串列表的单一函数的数据提供者开始。请看以下内容:

```
class SampleDataProvider {

    fun sampleList() = *listOf*<String>(
        "Apple",
        "Orange",
        "Mango",
        "Graps"
    )

}
```

现在让我们实现存储库及其实现，在其中我们应该从`SampleDataProvider`类中访问数据。下面看看它是如何工作的:

知识库及其实现

注意，如果您重新构建应用程序，您将得到一个编译时异常，因为我们要求 Dagger 在存储库实现中提供数据提供者实例，但没有告诉它如何提供。

# 在 Android 库中创建 Dagger 模块

这个编译时错误将我们引向库中的 agger 模块。在这里，我们需要创建一个带有函数的模块，用 binds 或 provide 来告诉 dagger 如何在调用点被请求时提供依赖关系。看看一个简单的匕首模块:

Android 库中的简单匕首模块

# Dagger 库组件

现在我们需要创建一个简单的 Dagger 组件，它接受库模块，并且应该从活动中注入。请看以下内容:

现在，为了触发 dagger 代码的生成，我们需要重新构建项目。一旦项目成功重建，我们将可以访问`**DaggerLibraryComponent**`**，并且我们可以在活动中实际执行注入。看一下代码:**

**仅此而已。现在您可以在活动中注入`SampleRepo`并从`DataProvider`类中访问数据。但是请记住，组件的范围仅限于这里的活动。看一下代码:**

**在库活动中注入依赖项的最终代码**

# **奖金**

**我最近看到了一篇由 [Satya Pavan Kantamani](https://medium.com/u/d255c4fc9d76?source=post_page-----4c9f44d04e68--------------------------------) 发表的关于 Android 中通过 Kotlin DSL 进行依赖管理的优秀文章，强烈推荐:**

**[](https://pavan-careers5208.medium.com/better-dependencies-management-using-buildsrc-kotlin-dsl-eda31cdb81bf) [## 使用 buildSrc + Kotlin DSL 实现更好的依赖性管理

### 依赖关系管理，实现更好的可重用性和易维护性

pavan-careers5208.medium.com](https://pavan-careers5208.medium.com/better-dependencies-management-using-buildsrc-kotlin-dsl-eda31cdb81bf) 

目前就这些。希望你学到了有用的东西。

感谢阅读。**