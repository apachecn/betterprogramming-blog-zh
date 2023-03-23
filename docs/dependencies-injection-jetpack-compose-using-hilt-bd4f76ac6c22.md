# 使用 Hilt 探索 Jetpack 组件的依赖注入

> 原文：<https://betterprogramming.pub/dependencies-injection-jetpack-compose-using-hilt-bd4f76ac6c22>

## 创建松散耦合的干净代码的技巧

![](img/d797663a1b7a8eb0bd29d95e4b45e0af.png)

恩伯·纳瓦罗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

您可以在下面找到以前关于 Jetpack Compose 的教程:

*   [通过构建应用程序一瞥 Jetpack Compose](/a-glimse-into-jetpack-compose-by-building-an-app-a7869723d4e8)
*   [在 Jetpack 组合中布局](/layouting-in-jetpack-compose-8c16e687bfe5)
*   [Jetpack 撰写中的导航](/navigation-routes-in-jetpack-compose-ce2e0337ed28)
*   [在 Jetpack Compose 中开始使用 MVVM](/mvvm-in-jetpack-compose-part-4-fe757a1a1b84)
*   [在 Jetpack 撰写中查看的视图模型](/viewmodel-to-view-in-jetpack-compose-7c7183b54fb5)

在本文中，我们将学习如何在 Jetpack Compose 中使用`Dagger-Hilt`为我们的应用程序注入依赖注入。

![](img/c7cbd2d77e191074e693b6a18f8b1df6.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是依赖注入？

依赖注入是一种设计模式，旨在通过形成控制反转来分离对构造对象和使用对象的关注，从而产生松散耦合的程序。

在 Android 中，有两种著名的依赖注入方法。`Koin`和`Dagger-Hilt`。关于选择什么的更多信息，你可以参考这里。

[](/dagger-hilt-vs-koin-dependency-injection-for-jetpack-compose-apps-2e598f00aff) [## 匕首/剑柄与锦鲤的 Jetpack 组成应用程序

### 匕首和锦鲤的优缺点

better 编程. pub](/dagger-hilt-vs-koin-dependency-injection-for-jetpack-compose-apps-2e598f00aff) [](https://medium.com/gradeup/dependency-injection-dagger-hilt-vs-koin-ab2f7f85e6c6) [## 依赖注入:匕首之柄 vs Koin

### 从零到英雄的依赖注入

medium.com](https://medium.com/gradeup/dependency-injection-dagger-hilt-vs-koin-ab2f7f85e6c6) 

使用`Dagger-Hilt`还是`Koin`并不重要，因为它们服务于相同的目的，但是在本文中，我将使用`Dagger-Hilt`。

# 我们开始吧

我们必须在应用程序中设置`Dagger-Hilt`。在项目级别打开`build.gradle`并复制以下代码:

打开`build.gradle` app level，复制这段代码:

创建`JetpackComposeApp.kt`并添加以下代码:

转到`AndroidManifest.xml`并将该属性添加到`application tag`中:

这就是我们在应用程序中设置`Dagger-Hilt`的方式，因为这是一个强制步骤。特别是你需要让`JetpackComposeApp.kt`有`@HiltComposeApp`注释来注释`Application`类，并通过在`AndroidManifest.xml`中定义`android:name=”.JetpackComposeApp”`来让那个`Dagger-Hilt`在我们的应用中被识别。

现在我们可以打开`MainActivity.kt`并添加以下代码:

这个注释使得`Dagger-Hilt`可以向`MainActivity.kt`注入依赖关系。注意，我们可以将`@AndroidEntryPoint`放置到`MainActivity.kt`中，因为`MainActivity.kt`是具有`HomeFragment.kt`和`DetailFragment.kt`等片段的地方。每个片段都与 MVVM 的`ViewModel`和`Model`有关。

在 MVVM `Model`板块，我们将在`Dagger-Hilt`制作模块，增加一些服务功能。创建`AppModules.kt`并添加以下代码:

由此，`AppModules.kt`在匕首-刀柄上标记为`@Module`级。此外，该类将使用`SingletonComponent`安装在应用程序级别。所以这个类将首先被执行，并且只要应用程序还在运行，它就将继续存在。

这样，我们可以实现对`AppModules.kt`的服务，该服务将分离紧耦合的代码，稍后将对此进行解释。

打开`GamesService.kt`和`GamesRepositoryImpl.kt`。看看下面的代码:

每次调用`GamesRepositoryImpl.kt`时，都会初始化`GamesServices.getInstance()`。这使得`GamesRepositoryImpl.kt`与`GamesServices.kt`紧密耦合。为避免这种情况，将服务代码移动到`AppModules.kt`，如下所示:

现在`AppModules.kt`将使用`retrofit`提供服务，并将服务注入到存储库中，如上面的代码所述。

因为服务和实现已经由`AppModule.kt`负责，我们将把`GamesService.kt`和`GamesRepositoryImpl.kt`改为:

现在`GamesRepositoryImpl.kt`将不会与`GamesServices.kt`紧密耦合。最重要的是，`GamesRepositoryImpl.kt`为`Dagger-Hilt`添加了`@Singleton`注释，以便知道这个类是`Singleton`类。

有关`Singleton`的更多信息，请参考此处:

[](https://www.patterns.dev/posts/singleton-pattern/) [## 单一模式

### 单例是可以被实例化一次的类，并且可以被全局访问。这个单一实例可以共享…

www.patterns.dev](https://www.patterns.dev/posts/singleton-pattern/) 

您甚至可以在`AppModule.kt`中注入`String`以及下面的代码:

注意，如果我们提供多个相同的数据类型，我们将使用注释`@Named`在`Dagger-Hilt`中进行区分。

所以我们只做了一些会影响`GamesRepositoryImpl.kt`和`GamesService.kt`的改动。到目前为止，它们的代码都是这样的:

在`ViewModel`部分，我们只需要声明`ViewModel`可以像这样被认定为匕首柄类。打开`HomeViewModel.kt`和`DetailViewModel.kt`，添加以下内容:

现在，`ViewModel`被宣布为匕首柄`ViewModel`级。

现在剩下的都在`View`部分了。只需打开`HomeFragment.kt`和`DetailFragment.kt`并改变这条线:

现在，重建应用程序，并开始运行它。您的应用程序将像以前一样运行。但是代码本身将是松散耦合的，也更加简洁。

> 这是不利的一面:任何时候你想改变与匕首之柄相关的职业，你都必须重建它。否则，它不会按预期运行。

# 结论

我们已经了解了如何使用 Jetpack Compose 中的`Dagger-Hilt`为我们的应用程序注入依赖注入。

这篇文章的所有代码可以在这里查看。

在下一篇文章中，我将讲述如何在我们的应用程序中使用模块化来划分我们的功能。