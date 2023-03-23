# 带有清理的 iOS 依赖注入:第 1 部分

> 原文：<https://betterprogramming.pub/ios-dependency-injection-with-cleanse-1ef495e390a2>

## 向您的类和结构注入它们需要的功能

![](img/4f1005a7e9861e1674ce34ec10d5f204.png)

由[马赫什·拉纳韦拉](https://unsplash.com/@mahesh_ranaweera?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

你好！

在这篇文章中，我们将谈论当今软件开发中的一个热门话题。我很确定你们大多数人都听说过依赖注入。

一个对象为另一个对象提供依赖关系。这在我们的代码中经常发生。例如，当我显示用户资料屏幕时，我的`UserRepository`类提供了所有用户的详细信息。但是 repository 类需要一个 HTTP 客户端(`URLSession`)来获取数据，会话需要一个配置，等等。这为所有这些对象实例化提供了依赖注入。

什么？这是一种模式吗？嗯，确实是。

我们是否在被动地使用这种模式，甚至有时不自知？嗯，是的。我们不断地使用对象为其他对象提供数据，当我们处理一个小的代码库并且一个开发人员必须做所有的事情时，这是非常简单的。但是，随着团队变大，代码库随之而来，这就成了一个问题。久而久之，我们必须处理混乱。

DI 是软件工程中一个非常古老的问题。大多数 web 框架很久以前就已经解决了这个问题，并提供了内置的机制来解决这个问题。这在一开始似乎不是一个移动问题，因为我们开发的应用很小，没有太多的功能。因此代码更少。现在，它变得比以往任何时候都更加突出，很明显需要这样的工具。

DI 框架分为两类:服务定位器和构造器注入。

第一种工作方式类似于一个桶，在桶中注册依赖项或实例化它们的方式，然后从桶中请求它们。以下是使用服务定位器的示例:

服务定位器示例— Swinject。

如果你在网上搜索 iOS DI 框架，你会碰到很多很棒的库，比如 [Swinject](https://github.com/Swinject/Swinject) 、 [Pure](https://github.com/devxoul/Pure) 、 [Dip](https://github.com/AliSoftware/Dip) 等。你也可以查看一下 [Typhoon](https://github.com/appsquickly/Typhoon) ，它曾经是 Objective-C 的事实上的 DI 框架，还有一个是我故意留在最后的:[clean](https://github.com/square/Cleanse)。

Cleanse 是一个库，灵感来自最著名的 Android DI 库， [dagger](https://github.com/google/dagger) 。有趣的事实:dagger 2 已经被 Google 重写了，但是版本 1 原本是 square 写的[。清流也是 square 写的。用过匕首的人会发现净化很熟悉。尽管它是为完全不同的框架和完全不同的语言编写的，但它在提供 dagger 的大多数功能方面做得非常好。](https://github.com/square/dagger)

匕首无论如何都不是简单的工具。这同样适用于清洗。

# 术语

## 组件和子组件

组件是在需要时注入适当对象的粘合剂。你的图表以一个`RootComponent`开始，这是你的图表将要存在的地方。

组件相当于 dagger 的组件。

## **模块**

模块是组件的构建块。它们是您定义如何实例化您的依赖项的方式。

模块相当于 dagger 的模块。

## 领域

范围是定义依赖关系生命周期的方式。目前，Cleanse 提供了两种作用域:singleton 和 unscoped。通过使用单例作用域，您告诉绑定器，您希望它在提供依赖关系时使用同一个实例。使用 unscoped 意味着每次都会实例化一个新的实例。

瞄准镜相当于匕首的瞄准镜。

## **类型标签**

标签是为我们的对象提供命名参数的一种方式。假设您有两个相同类的对象，您想为它们分配不同的用途:一个字符串是您的服务 URL，另一个字符串是 API 令牌。模块只知道类型，你的两个对象都是字符串。标签将区分这两个对象，您将能够使用您的依赖实例化所需要的那个。

标签相当于 dagger 的命名提供者。

## **房产注入**

顾名思义，属性注入是在对象实例化后为其提供依赖关系的方法。这在系统实例化对象时特别有用，比如用`AppDelegate`、`SceneDelegate`等。

## **辅助注射**

辅助注入是一种向对象实例化提供额外对象的技术，这些对象不是图的一部分。例如，用户选择了一个项目，您将使用该项目的详细信息来推送详细视图控制器。这可以通过辅助注射来实现。

## **多绑定**

多绑定是一个简单的概念，您提供的对象被放入一个数组中。正如在文档中提到的，字典和集合也是可能的，但是还不支持。

# 例子

你可以在 GitHub 上找到下面这个例子[的源代码。](https://github.com/g20ready/CleansePlayground)

这个项目使用 Xcode 11 的新`SceneDelegate`作为起点，没有故事板，但它适用于`AppDelegate`做了一些改变。[阅读更多关于场景代表](https://learnappmaking.com/scene-delegate-app-delegate-xcode-11-ios-13/)的信息。

创建一个新项目并将`pod 'Cleanse'`添加到你的 Podfile 中。这就是我们目前所需要的。现在，在您的项目中添加一个名为`MainComponent`的新文件，它继承了 Cleanse 的`RootComponent`，并将下面的代码粘贴到其中。

主组件。

*   `Root` 将是该组件构建的对象。
*   `Scope` 是该组件的图形对象的生命周期范围。
*   `Seed` 是我们将提供给组件的初始对象，以便组件构建其图形。
*   `Configure` 用于将模块包含到我们的组件中。
*   `ConfigureRoot` 是打造我们根的切入点。在我们的例子中，它是`SceneDelegate`的属性注入器。
*   `ConfigureAppSceneInjector`是一个帮助器方法，它将`BindingReceipt`返回给我们的属性注入器。

在组件的配置步骤中，我们看到两个模块。这些将为我们的组件提供它所需要的依赖。添加一个新文件，最好命名为`UIKitModule`，并添加以下代码:

正如这里所看到的，`UIKitModule`是一个通用模块，除了包含其他 UIKit 模块之外什么也不做，比如`UIWindow.Module`。一般来说，通过扩展现有的类或结构并在其中包含模块来为它们提供模块是一个很好的做法。`UIWindow`模块将一个`UIWindow`对象绑定到我们的依赖图，只要该图能够为我们定义的绑定函数提供依赖关系。所以这个绑定需要一个`UIViewController`类型的类型标签和一个`UIWindowScene`。如前所述，`UIWindowScene`是作为种子提供给我们的主组件的，所以我们只缺少一个`UIViewController`。

好了，现在来添加我们的`CoreAppModule`吧。它应该是这样的:

这个类将包括我们应用中定义的其他模块，但暂时只包括一个`UIViewController.Module`。它应该是这样的:

这部分提供了我们需要的`UIWindow.Module`**`ViewController`以便绑定代码可以运行。我们的依赖图需求现在已经完成了。我们现在缺少的是我们`SceneDelegate`的成员注入部分。**

**所以我们的主要组件还有两个功能。**

**把`configureRoot`看作是我们构建函数的助手。它接受一个根类型的`ReceiptBinder`并返回一个根类型的`BindingReceipt`。在方法体中，我们使用了`ReceiptBinder`的`propertyInjector`函数，它将我们的绑定转换为`PropertyInjectionReceiptBinder`。这个特殊的绑定器将查看作为注入器提供的函数的签名，并确保它具有构建我们的`ComponentFactory`所指定的类型。否则，它将失败，并显示一条错误消息，指明发生了什么问题。现在转到我们的`SceneDelegate`并添加以下代码:**

**我们这里只有两件事值得一提。首先是`injectProperties`函数。记住，我们用这个来告诉我们的组件`PropertyInjector`需要什么属性。该房产是由`UIWindow.Module`提供的`UIWindow`。其次是`setupApplication`。这个函数需要一个`UIWindowScene`对象，它是我们组件的种子。接下来，我们从要求我们的主要组件的`ComponentFactory`开始。如果我们的依赖项设置不正确，这将会失败，错误将会提供表单中出错的信息。**

```
error building the component factory: *** HNStory *** binding missing
   -> required by PropertyInjector<SceneDelegate> at DI/MainComponent.swift:28
```

# **结论**

**我知道这很难理解，尤其是如果您以前从未使用过依赖注入框架，所以我将在这里停下来，带着第 2 部分的清理回来。我们将附加我们的网络层，使用实际的视图控制器而不是一个空的视图控制器，并向您展示辅助注入——一个尚未发布到 CocoaPods 的特性，但是您可以在 branch `4.2.5-stable`中找到它。我也将表达我对是否值得在一个新的、中等的和大的代码库中使用 Cleanse 的想法。**

**如果你发现任何不清楚的地方，请在下面的部分随意评论。我会试着给你一个解释。**

**感谢您花时间阅读本文。一旦你觉得准备好了，继续这个系列的第二部分。**