# 在 iOS 中将具体的协调器从导航系统中分离出来

> 原文：<https://betterprogramming.pub/decoupling-concrete-coordinators-from-your-navigation-system-in-ios-27f39f8e8d8>

## 如何使用通用工厂从协调器实例化中分离导航

![](img/ae7e29d76a50fa3000615404df1e6e17.png)

[Goran Ivos](https://unsplash.com/@goran_ivos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

MVVM 是实现应用最常见的架构之一。这个架构的目标是通过将表示逻辑转移到另一个组件`ViewModel`来减轻`ViewController`的责任。

MVVM 没有直接解释如何解决从一个`ViewController`到另一个【】的导航问题。这个问题如此重要，以至于 MVVM 的一个变种被开发出来:MVVM-C

MVVM-C 架构通过引入一组新的组件`Coordinators`解决了这个问题。他们唯一的责任是正确处理导航。

实际上，每当一个`ViewController`完成它的任务，它就调用`ViewModel`的一个方法，要求`Coordinator`转到下一个屏幕。

这种架构是可行的，所有的职责都是明确定义的。部件划分整齐隐蔽:`ViewControllers`不知道其他`ViewControllers`的存在`ViewModels`不知道其他`ViewModels`的存在。

`Coordinators`是了解 app **中不同`Coordinators` 的组件。**他们像胶水一样工作，把所有东西粘在一起。

尽管有这些优点，还是有一些缺点值得考虑:

1.  如果使用一个协调器从特性 A 移动到特性 B，它应该在哪个模块中？
2.  为什么生活在不同模块中的`Coordinators`应该相互了解？

在今天的文章中，我想展示我们如何用一组合适的工厂对象来解决这些问题。

# 开始的情况

让我们在实现建议的解决方案之前，先看看代码在开始时是什么样子。

我们将使用一个示例应用程序，这是一个餐馆预订应用程序。主屏幕是一个表单，用户可以在其中设置一些搜索标准。当搜索被发出时，我们给出一个带有加载器的模态视图控制器。当(模拟的)网络调用完成时，我们转到一个显示餐馆列表的屏幕。

![](img/232bc572774e61c6061f6dee4754884c.png)

我不会深入所有的屏幕细节，也不会在这里报告全部代码:我在本文的*结论*部分添加了存储库。

让我们把注意力集中在`RootCoordinator`上，看看导航是如何编排的。

在这个代码片段中，`RootCoordinator`与其他协调器紧密耦合。它也直接呈现了`RootViewController`。这违反了 [**依赖倒置**](https://en.wikipedia.org/wiki/Dependency_inversion_principle) 原则。

依赖倒置原则声明代码不应该依赖于对象的实现细节，而应该只依赖于它的接口。在这种情况下，我们严格依赖于`ViewController`和另一个`Coordinators`的具体实例。

如果我们想要呈现一个不同的具体的协调器或视图控制器，我们需要更新`RootCoordinator`代码来改变哪个组件被创建和呈现。

我们没有任何方法来控制哪个具体的`ViewController`或`Coordinator`被创建。这可能会使编写我们的单元测试变得困难。

此外，如果我们有一个组件，一旦出现，它会立即执行一些指令，它们就会被执行，即使我们在一个测试环境中，我们并不想实际执行它们。

最后，`RootCoordinator`知道`LoadingCoordinator`和`RestaurantsCoordinator`的存在，尽管它们驻留在其他模块中。我们不想泄露这些知识。

> 注意:这些问题也可能出现在层级结构中。例如，`RestaurantsCoordinator`可能知道一个`RestaurantCoordinator`或`BookingCoordinator`，即使它们是在其他模块中定义的。

# 使用工厂解耦协调器

为了解耦`Coordinators`，我们需要将需要创建哪个组件的决定推到应用程序的组合级别。

这可以通过向`Coordinators`注入工厂来实现。我们架构的最佳方法是在`Dependencies`值中注入一个`CoordinatorFactory`。

第一步是为所有的`Coordinators`定义一个公共协议，让我们让所有的`Coordinators`都遵守它:

现在，我们需要创造能够创造我们的`Coordinators`的工厂。我们需要为每个协调员创建不同的工厂。所有的工厂必须有一个公共接口，所以我们需要引入一个新的协议:

从这个片段中，您可以看到我引入了一个关联类型`R`，它必须符合一个名为`Requirements`的协议。该协议是一些配置对象的标记。这个对象允许我们统一所有`Coordinators`的接口。

具体来说，这些是`Requirements`的实现:

这些值只包含数据，没有附加逻辑。而且，这些是我们模块的公共接口的一部分，它们是公共的。在`Requirement`中使用的所有类型都是公共的，并且`Requirement`不公开任何特定于模块的类型。

每个模块只暴露另一个`public`组件:T10。其他的一切(视图控制器、视图模型、协调器等等)都是私有的，可以留在相应的模块中，而不需要泄漏它。

# 使用工厂

既然我们已经有了所有的碎片，我们可以试着把它们拼在一起。第一个想法可能是传递依赖关系中所有不同的工厂。

代码看起来会像这样:

我们看到`Dependencies`值包含了我们在上一步中创建的所有工厂。我们在`AppDelegate`中使用这些工厂来创建`RootCoordinator`，在`RootCoordinator`中呈现装载和餐馆屏幕。

这种方法是可行的，但是它违反了 [**开/关原则**](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) **:** 一个软件应该对扩展开放，但对修改关闭。在这种情况下，如果我们想引入一个新的`Coordinator`，我们将需要修改依赖对象，但没有必要这样做。

我们可以通过添加一个`CoordinatorFactoryProvider`图层来解决这个问题。这是一个单一的值，给定一些需求，可以为我们创建一个合适的工厂。

这个额外的层只是接受一个绑定到工厂的需求。这是允许的:`Requirement`和`Factory`位于同一个模块中，它们是模块本身的公共接口。

我们用`Provider`创建的工厂符合`CoordinatorFactory`协议，因此它将能够根据需求创建正确的`Coordinator`。

通过这一更改，我们可以按如下方式更新我们的依赖项和调用站点:

我们的依赖性已经缩小，我们不必在我们的`Coordinator`中添加对不同工厂的访问。一切都是由我们传递给`CoordinatorFactoryProvider`的需求驱动的。

如果我们想要为一个新的`Coordinator`添加一个新工厂，没有必要做任何改变:我们可以在需要的地方创建需求对象，并使用`CoordinatorFactoryProvider`为它检索一个合适的工厂。

# 改进模块化

目前的解决方案仍然不是最佳的。`RootCoordinator`正在使用来自`Loading`和`Restaurants`模块的一些公共类型。这意味着`Root`模块与这些其他模块紧密耦合。

如果明天我们想用不同的模块替换`Loading`模块，如果要求或工厂发生变化，我们也必须更新`RootCoordinator`。

一个解决方案可以是移动`Composition`模块中的`Factories`和`Requirements`，其中定义了所有协议和`CoordinatorFactoryProvider`。通过这样做，假设`RootCoordinator`已经依赖于那个模块，我们就不会受到其他模块变化的影响。如果一个新实现的模块使用相同的`Requirements`，我们只需要更新`Composition`模块。

然而，我们可以通过向`CoordinatorFactoryProvider`添加一些预定义的方法来进一步推动解耦。

我们正在添加一些方法来创建我们在`CoordinatorFactoryProvider`中需要的特定协调器。如果我们看看调用站点，情况有了很大的改善，更加清晰，现在`Root`模块不再依赖于其他模块。

这最后一步可能看起来像是倒退。我们牺牲了一些更抽象的行为，换来了一个不那么通用的行为。然而，如果我们考虑这一点，这并不是倒退。

1.  我们的`RootCoordinator`和`AppDelegate`对其他`Coordinators`的具体类型一无所知。
2.  通过只作用于`CoordinatorFactoryProvider`，我们可以改变返回的协调器的具体类型。
3.  仍然不违反**开/关原则。**如果我们想要添加新的协调者，我们不会被强制添加其他特定的方法。
4.  这种方法推动了重用:假设餐馆模块必须呈现`Loading`屏幕。`Restaurants`模块从`CoordinatorFactoryProvider`那里得到了它所需要的一切，它不必担心模块所需要的具体类型。

# 结论

在今天的文章中，我们探讨了如何通过利用一些工厂对象和[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)原则来分离我们的应用程序的协调器结构。

我们走了很长一段路才获得一个非常简单的结果，导致特定架构决策的过程比展示最终结果重要得多。

最后，我们需要做出一个权衡取舍的决定:是使用更多耦合模块的通用代码更好，还是使用更明确、更清晰的代码库来推动非耦合模块更好，即使添加新组件可能需要修改组合根？

这种方法(和这种解决方案！)可以应用于其他环境:`ViewControllers`和`ViewModels`经常会出现类似的问题，尤其是当我们决定不使用协调器的时候。

完整的项目可以在这个[库](https://github.com/cipolleschi/restaurants)中找到。`[main](https://github.com/cipolleschi/Restaurants/tree/main/Restaurant)`分支包含重构前的代码。[feat/coordinator-refactoring](https://github.com/cipolleschi/Restaurants/tree/feat/coordinator-refactoring/Restaurant)包含了重构后的代码。为了检查差异，我打开了一个从`feat/coordinator-refactoring`分支到`main`分支的 [PR](https://github.com/cipolleschi/Restaurants/pull/1) ，这样就可以更详细地探索变化。