# 使用简洁的架构设计 Android 中的基础组件

> 原文：<https://betterprogramming.pub/design-the-base-components-in-android-with-a-clean-architecture-ff803fad5895>

## Android 的干净架构教程的第 4 部分

![](img/d15a67ea24f312b53f3f8f53ca35b22c.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Federica Galli](https://unsplash.com/@fedechanw?utm_source=medium&utm_medium=referral) 拍摄的照片。

这是我们关于使用合适的干净架构构建 Android 应用程序的系列文章的第四部分。在这一期中，我们将关注 Android 组件的基类和集中公共工作的`ViewModel`。

# 快速回顾

在继续之前，我强烈推荐阅读本系列的前几部分，在那里您将了解为什么我们应该使用干净的架构，如何使用 Dagger2 实现依赖注入，等等。

*   [Android 清洁架构教程(第 1 部分):如何用 Dagger2 建立复杂的 DI 结构](/how-to-set-up-a-dependency-injection-framework-with-dagger2-in-android-aa23ee9299db)
*   [Android 的干净架构教程(第 2 部分):使用协程创建存储库和用例](/creating-a-repository-data-layer-with-kotlin-coroutines-in-android-1375ab79b6f0)
*   [安卓清洁架构教程(三)](/injecting-android-viewmodels-with-dagger2-in-clean-architecture-744c1fe81530) `[ViewModels](/injecting-android-viewmodels-with-dagger2-in-clean-architecture-744c1fe81530)` [与匕首 2:天作之合](/injecting-android-viewmodels-with-dagger2-in-clean-architecture-744c1fe81530)
*   Android 的干净架构教程(第 4 部分):你在这里。

在这一部分，我们将关注于创建基类，在那里我们将保留所有的常见任务，比如创建一个`ViewModel`实例，膨胀一个布局，等等。

我们必须为每个 Android 组件和其他组件(如`ViewModels`和`ReyclerView`适配器)创建一个单独的基类。

不要再耽搁了，让我们开始吧。

# 基础活动

在 Android 应用程序中，`Activities`是一些最重要的组件。Android `Activity`是当用户点击应用图标或者任何第三方应用想要访问或触发你的应用 UI 时启动应用的入口点。

像任何其他 Android 组件一样，`Activity`也有生命周期。在`onCreate`函数中，我们需要使用`setContentview`函数来扩展`Activity`布局。像这样的常见功能应该转移到基本组件中。为了更好地理解它，让我们看看完整的基类代码:

如果你观察代码，你会发现`BaseActivity`是一个抽象类，因为我们需要包含可以在实际活动中被覆盖的抽象函数，在实际活动中，我们将使用它们实现各自的功能。

在这个课程中，你会看到三个抽象函数。`getInjectViewModel`、`setupViews`和`getLayoutResId`分别用于创建`ViewModel`实例、触发特性级功能和展开布局。

在类的底部，你会看到三个普通的函数:`takeToPlaystore`、`openUrl`和`showAlert`。这些是用于执行一些常见任务的功能，如导航到 playstore 以对应用程序进行评级或显示简单的警告对话框。

这里，我们还维护了一个与活动的生命周期相关联的`coroutinescope`。当你在做一些需要花费时间的事情时，我们可以很容易地使用这个范围。当`onDestroy`函数被触发时，协程内执行的所有任务都将被取消。

## 使用

现在让我们看看如何使用`BaseActivity`。看一看:

首先，我们使用 [Koin](/koin-a-kotlin-native-dependency-injection-library-8e13b81bd48e) 或 [Dagger2](/dependency-injection-in-android-with-dagger2-d260b8a72bb0) 依赖注入库将`HomeActivity`扩展为`ViewModel`的类型。然后我们必须覆盖基类的抽象函数，并传递各自的布局和`ViewModel`。一旦一切都配置好了，就会调用`setupViews`函数，我们就可以开始实际的编码了，比如设置监听器或执行网络请求。

# 碱基片段

在现代 Android 开发中至关重要。随着单个`Activity`应用的增长，`Fragments`的重要性也迅速增加。基本上，在现代 Android 开发中，我们只使用`Activities`来启动应用。剩下的工作是使用`Fragments`完成的。

与`Activity`类似，`Fragment`也有生命周期。在`onCreateView`函数中，我们需要展开视图，并开始执行`onViewCreated`函数中特定于特性的代码。看一看:

与`BaseActivity`非常相似，`BaseFragment`也是一个抽象类，它有一个抽象函数来创建`ViewModel`实例，展开布局，并触发特定于功能的代码。

除此之外，我们还有一些实用功能，如`rateUs`、`moreApps`、`openURL`等等。如果应用程序主要依赖于`Fragments`，那么在基础`Fragment`中使用公共函数是有意义的。与`BaseActivity`类似，我们维护一个协程范围，并在片段分离时取消它的所有子任务。

## 使用

现在让我们看看如何使用`BaseFragment`:

与`HomeActivity`一样，我们必须用`BaseFragment`扩展`Homefragment`，使`ViewModel`的类型能够注入。然后我们必须覆盖基类的抽象函数，并传递各自的布局和`ViewModel`。一旦一切都配置好了，就会调用`initViews`函数，我们就可以开始实际的代码了。

# BaseViewModel

与其他类相比，这是最简单的基类。这里，我们将像在上面的类中一样创建一个协程作用域，但是调度程序将是`Dispatcher.IO`而不是`main`或`default`。

在`ViewModel`中，我们主要执行与后台相关或长期运行的任务，比如发出网络请求、文件相关操作、数据库事务等等。所有这些操作都应该在后台完成，所以我们使用的是`Dispatchers.IO`。看一下代码:

由于我们没有任何抽象函数需求，所以我们没有将`BaseVoewmodel`声明为抽象的。但是我们需要使用一个 open 关键字。在 Kotlin 中，默认情况下类是 final，这意味着它们是不可扩展的。我们需要使用如上图所示的`open`。

## 使用

现在让我们看看如何使用`BaseViewModel`。看一看:

和上面的两个基类一样，我们用`BaseViewModel`扩展了`HomeViewModel`。然后，如果您想要进行网络请求或数据库事务，我们使用来自`BaseViewModel`的`coroutineScope`来完成，这将任务的范围限制在`ViewModel`的生命周期内。

# 奖金

## GitHub 回购

导入项目并切换到`articles/viewmodels`分支，查看与本文相关的代码:

[](https://github.com/SG-K/Clean-Architecture-Sample) [## SG-K/Clean-架构-示例

### 在 GitHub 上创建一个帐户，为 SG-K/Clean-Architecture-Sample 开发做贡献。

github.com](https://github.com/SG-K/Clean-Architecture-Sample) 

要了解有关高级 Android 开发和架构组件的更多信息，请阅读以下文章:

*   [在 Android 中使用视图模型的最佳实践](/best-practices-for-using-viewmodels-in-android-e986b25dc78f)
*   [科特林的存储库中不再有实时数据](/no-more-livedata-in-repositories-in-kotlin-85f5a234a8fe)
*   [使用 Kotlin 进行高级编程](/advanced-android-programming-with-kotlin-5e40b1be22bb)
*   [Android Clean 架构详细指南](https://medium.com/android-dev-hacks/detailed-guide-on-android-clean-architecture-9eab262a9011)

目前就这些。希望你学到了有用的东西。感谢阅读。