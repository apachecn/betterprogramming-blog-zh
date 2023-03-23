# 如何在 Android 中用 Dagger2 建立依赖注入框架

> 原文：<https://betterprogramming.pub/how-to-set-up-a-dependency-injection-framework-with-dagger2-in-android-aa23ee9299db>

## 洁净建筑导论

![](img/719089109ee15eb527e4e9a41771a4a1.png)

卢卡·布拉沃在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在我职业生涯的早期，我认为尽快完成一项任务是让你成为一名高效开发人员的原因。我相信你们很多人也这么想，但是你知道[他们怎么说](https://www.goodreads.com/quotes/87111-good-judgment-comes-from-experience-and-experience-comes-from-bad):

> "好的判断来自经验，而经验来自坏的判断."

快进到今天，我是一名有五年经验的 Android 开发人员。我从自己的经验中学到的最重要的一课是，拥有合适的架构和按时完成工作一样重要。

从现在起的一天或一年后，您可能会陷入无法维护代码、模块之间紧密耦合或者重复编写相同代码的境地。

解决所有这些问题的一个简单方法是在编写代码时保持谨慎。这可能是显而易见的，但肯定不容易。开发人员必须学习很多概念，比如:

*   依赖注入
*   存储库模式
*   基础组件
*   状态管理

当你开始实现代码时，你会遇到核心问题，并学习新的东西来解决它们。这个系列是关于使用我多年来学到的所有概念来构建一个具有合适的干净架构的 Android 应用程序。这将是一个漫长的系列，所以系好安全带。

# 为什么要清洁建筑？

1.  关注点的分离——将代码分离成不同的模块或具有特定职责的部分，使得维护和进一步修改更加容易。
2.  松散耦合—灵活的代码。不改变体制，什么都可以改变。
3.  易于测试——由于模块之间的松散耦合，我们可以轻松地单独测试组件。

如果你是清洁架构的新手，有很多关于这个主题的文章。我推荐读这本书:

[](https://medium.com/android-dev-hacks/detailed-guide-on-android-clean-architecture-9eab262a9011) [## Android 清洁架构详细指南

### 编写 Android 应用程序的最佳方式

medium.com](https://medium.com/android-dev-hacks/detailed-guide-on-android-clean-architecture-9eab262a9011) 

# 我们将学到什么

正如我所说，这将是一个冗长的系列，我们将涵盖从 DI 到状态管理的所有内容。在第一部分中，我将用 Dagger2 介绍依赖注入设置。

在以后的部分中，我们将介绍带有协程的存储库设置和用例、基本组件、带有密封类的状态管理，以及一些实用程序和扩展，它们将使您的生活变得简单。

如果我错过了以上列表中的任何东西，请留下评论。我将试着把它们作为本系列的连续部分来讨论。

# 用 Dagger2 进行依赖注入

依赖注入是一种在需要时提供类的实例的方法，而不是在没有任何样板代码的情况下创建它。DI 的主要优势是代码的可重用性和资源的有效利用。 [Dagger2](https://github.com/google/dagger) 是 Android 开发中用来实现 DI 的著名库。

这是 Dagger2 的高级设置。我想你应该熟悉 DI 和 Dagger2。如果没有，请停止阅读并浏览下面的文章:

[](https://medium.com/better-programming/dependency-injection-in-android-with-dagger2-d260b8a72bb0) [## Android 中的依赖注入与 Dagger2

### 了解你需要知道的关于 Dagger2 的一切

medium.com](https://medium.com/better-programming/dependency-injection-in-android-with-dagger2-d260b8a72bb0) 

## 综合

要将 Dagger2 集成到您的项目中，请在应用程序级`build.gradle`文件中的`dependencies`标签下添加以下库:

```
implementation **"com.google.dagger:dagger-android:2.17"**
implementation **"com.google.dagger:dagger-android-support:2.17"**
kapt **"com.google.dagger:dagger-compiler:2.27"**
kapt **"com.google.dagger:dagger-android-processor:2.17"**
```

# Dagger2 模块

在创建模块之前，我们需要知道在我们的 Android 组件中需要哪些依赖对象。比如做一个网络请求，我们需要`OkHttp`和序列化依赖(`Gson`、`Moshi`等)。)对象，这些对象有助于创建一个`Retrofit`实例。如果您使用 Firebase 进行分析，我们需要一个`FirebaseAnalytics`实例来记录事件。

对于任何 Android 项目来说，这些都是必需的。您可以拥有特定于项目的依赖关系。例如，如果您的应用程序支持离线，那么您可能希望实现房间数据库，因此我们需要提供 Room 的抽象类实现作为依赖项。

我们省略了一些必要的依赖组件，比如首选项助手、存储库、改进的 API 服务接口等等。我们将在接下来的文章中关注它们的依赖注入。

在根包下创建一个`di`包，然后添加下面的模块。在创建以下模块之前，您需要复习一些概念:

*   `@Module`:用于创建依赖类对象的类。
*   `@Provides`:用于模块类内部的方法，提供依赖关系。
*   `@Binds`:类似于`provides`，但是它被表达为一个抽象方法，并且只有一个参数，这个参数应该等于它的返回类型(主要是指它的一个实现)。与`provides`不同的是，`binds`不会为每个函数生成工厂类，这将减少 40%的代码。

## 网络模块

网络模块包含整个项目中与网络相关的所有依赖项，如`OkHttp`、`Retrofit`、`Gson`、`Firebase`等。我们需要创建一个名为`NetworkModule`的 Kotlin 类，然后用`@Module`对其进行注释。看一看:

```
**@Module**
class NetworkModule {}
```

在这个类中，我们需要创建前面提到的所有依赖项。首先，尝试创建不依赖他人的依赖关系。然后创建依赖于已创建对象的依赖对象。这样，您就不会错过我们创建任何其他依赖对象(如`Retrofit`)所需的任何依赖(如`OkHttp`和`Gson`)。看一看:

## 数据模块

一旦我们完成了网络模块，就该创建数据模块了。数据模块包含体系结构的数据层中存在的依赖关系。因此，在下一部分中，当我们试图创建一个存储库和用例时，我们将处理数据模块。

现在，只需创建一个名为`DataModule`的 Kotlin 类，并包含`NetworkModule`:

```
**@Module**(**includes** = [**NetworkModule**::class])
abstract class **DataModule** {}
```

## AppModule

这里，我们必须创建另一个名为`AppModule`的 Kotlin 类，并用 Dagger 的`@Module`注释对其进行注释。在这里，我们将包括像`context`这样的依赖关系。如果您正在使用 RxJava，我们可以提供调度程序等等。看一看:

## AppActivityBindingModule

我们必须创建一个模块，以确保所有的 Android 组件，如`Activities`，可以注入我们创建的依赖关系。Dagger 提供了开箱即用的`ContributesAndroidInjector`注释来实现这一点。我们需要创建一个抽象函数，其返回类型是所需的活动，并用`ContributesAndroidInjector`注释该函数。

`ContributesAndroidInjector`为此方法的返回类型生成一个`AndroidInjector`。注射器由一个匕首`subcomponent`实现，并将成为匕首模块的子模块。它应该只应用于抽象函数。

好的，这个和`Activities`一起工作很好。你可能会对`Fragments`感到疑惑。这就是事情变得有点棘手的地方。你注意到上面代码中的`@ActivityScoped`注释了吗？不是匕首图书馆的。我们需要创造这个范围。这里，我们必须创建三个范围:

1.  `ActivityScoped`
2.  `FragmentScoped`
3.  `NestedFragmentScoped`

我们在返回各自 Android 组件的 Dagger 模块类中的各自函数上使用这些作用域。看一看:

然而，问题是如何为`Fragments`创建 Dagger 子组件。简单地说，我们需要为您在特定的`Activity`上展开的每组`Fragments`创建一个模块类，然后将这个模块包含在那个`Activity`的抽象方法中。让我们为`Intro` `Activity`创建一个简单的模块类，它有两个`Fragments` ( `IntroHomeFragment`和`OtpVerificationFragment`)。看一看:

然后，我们需要将`IntroModule`包含在`AppActivityBindingModule`内其各自的`Activity`抽象函数中:

至此，我们完成了 Dagger 模块的基本实现。创建具有特定名称的不同模块的主要原因是为了保持整洁的结构。

# Dagger2 AppComponent

模块类不会直接提供依赖关系。相反，它将创建一个接口，作为`@Module`和`@Inject`之间的桥梁。

我们需要创建一个名为`AppComponent`的 Kotlin 接口类，并用`AndroidInjector<ApplicationClass>`扩展它。由于它是一个应用程序组件，我们需要确保它只使用`@Singleton`注释创建了一次。然后我们还需要用`@Component`进行注释，并包含所有的模块。看一看:

# 触发代码生成

这是最后一步。至此，我们完成了 Dagger 模块和组件的设置。现在我们必须在应用程序启动时触发 Dagger 组件生成。为此，我将使用`DaggerApplication`扩展应用程序类，然后覆盖`applicationInjector`函数，我们将在其中初始化`AppComponent`。

请记住，一旦我们完成了模块和组件，我们需要重新构建项目，以便 Dagger 生成代码。除非你这样做了，否则`DaggerAppComponent`不会生成。看一下实现情况:

# 奖金

## GitHub 回购

导入项目，切换到`branch articles/dagger_setup`可以看到与本文相关的代码:

[](https://github.com/SG-K/Clean-Architecture-Sample) [## SG-K/Clean-架构-示例

### 在 GitHub 上创建一个帐户，为 SG-K/Clean-Architecture-Sample 开发做贡献。

github.com](https://github.com/SG-K/Clean-Architecture-Sample) 

要了解有关依赖注入的更多信息，请阅读以下文章:

*   [“Android 中的依赖注入与 dagger 2”](https://medium.com/better-programming/dependency-injection-in-android-with-dagger2-d260b8a72bb0)
*   [“Hilt——一个新的 Android 依赖注入库”](https://medium.com/better-programming/hilt-a-new-dependency-injection-library-for-android-e6e00e719aeb)
*   [“Koin—kot Lin 原生依赖注入库”](https://medium.com/android-dev-hacks/koin-kotlin-native-dependency-injection-library-f1daddc1ef99)
*   [“Koin—一个 Kotlin 原生依赖注入库(第二部分)”](https://medium.com/better-programming/koin-a-kotlin-native-dependency-injection-library-8e13b81bd48e)

目前就这些。希望你学到了有用的东西。感谢阅读。