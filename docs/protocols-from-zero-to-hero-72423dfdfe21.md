# 如何掌握 Swift 中的协议

> 原文：<https://betterprogramming.pub/protocols-from-zero-to-hero-72423dfdfe21>

## 协议定义、继承、扩展等等

![](img/b8609c834ea9c16cc85eb9fc05a35964.png)

斯文·米克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

协议像许多其他语言一样表示接口。您可能已经知道，协议用于定义“适合特定任务或功能的方法、属性和其他需求的蓝图”

在本文中，我们将深入讨论使用 Swift 5.3 的协议。让我们以协议为基础开始吧。

# **确认协议**

协议允许您对相似的方法、功能和属性进行分组。Swift 允许您在`class`、`struct`和`enum`类型上指定这些接口保证。只有`class`类型可以使用基类和来自协议的继承。

在值类型和引用类型中符合协议

# 协议属性和功能

当在协议中定义属性时，我们必须指定它的类型，因为一致性类型不能推断属性类型。同样的，我们不能指定属性是存储的还是计算的属性，这就把规范留给了一致性类型。

我们还必须通过使用`get`和`set`关键字来指定属性是只读的还是可读写的。

协议中不允许属性和方法参数的默认值。

当一个值类型符合我们的协议，并且这个协议有一个修改它所属的实例的函数时，我们必须用关键字`mutating`作为函数定义(在协议内)的前缀。

如果一个引用类型(类)符合带有`mutating`关键字的那个函数，一旦我们在类中实现了那个 func，我们就不需要写`mutating`关键字。

协议属性和功能

我们来举个例子:

协议构造变化

正如我们所看到的，真正的只读协议应该符合常量(`let`)或计算属性。

协议也可以定义可选的属性和功能，但是需要用`objc`属性来标记协议和属性和/或功能。

任择议定书

结构和枚举不能采用标有`objc`属性的协议。只有类可以采用这些协议。现在，让我们来看看最好的协议特性。

# 协议继承

在 Swift 中，协议可以继承一个或多个附加协议。让我们看看如何使用它以及为什么要使用它。

用户协议

这里我们有一个`User`协议，每次我们添加一个新的`User`需求，这个协议都会变得更大。我们如何解决这个问题？让我们使用协议继承来使它变得更好一点。

使用继承的用户协议

然后，一旦我们创建了一个符合`User`协议的类型，我们必须实现`User`协议中定义的需求，以及`UserIdentity`协议中定义的需求。

符合用户协议的结构

这是开始设计我们代码的好方法，现在让我们看看协议的其他伟大特性。

# 协议组成

现在我们知道了如何使用协议继承，让我们看看下一步。

引用类型和 OOP 范例的主要缺点是类只能从一个超类继承。相反，对于协议和 POP，您可以使用协议组合，这是一个让我们的类型采用多种协议的特性。

让我们看一个例子:

在单元格中使用对象和委托的常见示例

现在，让我们在结构中做一些改变，是时候使用协议了:

使用协议组合的相同示例

我们可以看到这种方法的一些好处。首先，我们有一个`LocationCoordinate`协议可以随时使用。例如，如果我们将我们的应用程序从餐馆扩展到超市，这个新类型可以从`LocationCoordinate`继承来获得坐标属性。

让我们看到一个巨大的好处。比如，看到并想象一下`getDirections` func 会是什么样子。我们共享一家餐厅，可能只是召唤像地图或谷歌地图这样的 API。

我们一直在共享整个对象(这意味着对象具有所有属性)对于这个特性，我们可能会想，我们真的需要所有这些属性吗？如果答案是*否*，请查看该解决方案:

使用协议隔离

我们已经为我们的`RestaurantCellDelegate`更改了获取方向功能，所以现在我们将只共享`getDirections`功能显示去那家餐馆的方向所需的`Restaurant`属性和功能。

现在，我将用一个例子来描述最后一个巨大的好处。假设我们在新的`ViewController`中实现了`RestaurantCellDelegate`，其中`ViewController`只需显示和处理与预订相关的一切。

符合 RestaurantCellDelegate 的视图控制器

我们不会从我们的委托中实现`showMenu`、`getDirections`和`markFavorite`函数。

这里我们有两个选择，在我们的`RestaurantCellDelegate`中使用`@objc`和可选关键字，或者使用协议继承和组合。

正如我们已经知道的，结构和枚举不能采用标有`objc`属性的协议，所以我们只剩下一个选项。

避免@objc 可选的协议组合

现在我们的`ViewController`将看起来像这样:

符合没有未使用功能的协议的视图控制器

正如我们所见，协议组合允许我们将需求分解成许多更小的组件，而不是从单个协议或单个超类继承所有需求。

这允许我们的类型家族在宽度上而不是高度上增长，这意味着我们避免创建臃肿的类型，这些类型包含并非所有符合的类型都需要的需求。

总是用一两个需求来评估协议的使用，这将导致处理一个难以维护和管理的设计。

但这不是规则。在某些情况下，例如委托，我们只需要一个或两个函数的协议，在编写代码之前进行评估。

# 协议类型

协议只是蓝图而不实施任何东西的事件，它们是 Swift 中的类型。

这意味着我们可以使用协议作为参数、函数的返回类型、var 和 let 属性，以及在集合(如数组)中，正如我们在前面的例子中看到的，在我们的委托、单元和/或`ViewController`中使用了`Restaurant`协议。

现在，让我们深入了解一下这个概念:

一个餐馆实现

一个餐馆实现

我们已经创建了两个餐馆实现，一个是鸡肉餐馆，另一个是海鲜餐馆。假设我们需要在应用程序中显示所有餐馆的菜单。

一个操场文件使用我们的 func 显示菜单

正如我们所看到的，我们在任何地方都使用协议作为类型，我们可以使用它的特定实例，比如`ChickenFoodRestaurant`或`SeaFoodRestaurant`。

最后，这些是该函数的日志。

![](img/9ff1842827fe4cb9d6e2f093f91508ca.png)

显示菜单日志

这个特点把我们引向下一个话题。

# 协议多态性

*多态性*一词来源于希腊语 *poly* (意为*多*)和 *morphe* (意为*形成*)。

多态性让我们通过一个单一的、统一的接口与多种类型进行交互。在面向对象的编程世界中，单一的统一接口通常来自超类，而在面向协议的编程世界中，单一接口通常来自协议。

正如我们在上一节中看到的，我们在一个数组中使用了一个`ChickenFoodRestaurant`和`SeaFoodRestaurant`，但是两者都有一个特定的显示菜单 func 的实现。

这就是多态和协议协同工作创建不同实现的潜力，而不需要强制类型转换或重复代码。

# 协议类型转换

类型转换是一种检查实例类型和/或将实例视为指定类型的方法。

在 Swift 中，我们使用`is`关键字检查一个实例是否属于特定类型，使用`as`关键字将一个实例视为特定类型。

类型转换是关键字示例

如果对象是特定的类型，`is`关键字返回`true`。

作为关键字的类型转换示例

这个关键字让我们对协议进行类型转换，以验证一个对象是否属于特定类型，并将其用作该类型，正如我们在示例中看到的那样。

# 协议泛型

如果正确使用泛型和协议来避免重复代码，它们可能是一个强大的工具。假设您对泛型有一个大致的了解，让我们快速地看一下如何在协议中使用它。

协议泛型说:“我们不知道要使用的确切类型；因此，当一个类型采用这个协议时，它就会定义它。”

协议可以使用一个或多个`associatedtype`。让我们看一个例子:

具有一般项目的数据源协议

因此，一旦我们需要一个特定的数据源，例如，一个餐馆数据源，我们有几个选择:

一种具体的数据源类型

通用数据源类型

让我们看看两个数据源的运行情况:

使用数据源

两者都是一样的，但是一旦我们以这种方式设计我们的应用程序，就会有一些不同。这个话题我们以后再说。

# **协议扩展**

协议扩展使协议成为一个了不起的工具。它们允许我们添加协议功能和属性的默认实现和值。

正如您可能注意到的，它是一个有用的工具，可以让需求变得可选，而不需要`@objc`和 optional 关键字。符合协议的类型可以提供自己的实现，也可以使用默认的实现。

如果你熟悉 decorator 模式，你可能知道协议扩展允许我们添加协议中没有描述的 funcs 的实现，“装饰*”*它。

一个很棒的特性，可以在不修改每个类型的情况下将特定的函数添加到多个一致性类型中。你看到我们在这里如何杀死经典的基类继承了吗？

让我们看一个所有这些特性修改我们之前声明的`Restaurant`协议的例子:

使用扩展添加默认值和实现

我们已经更改了我们的`Restaurant`协议，为菜单属性添加了默认值，并为`displayMenu`添加了默认实现。让我们看看具体类型是如何变化的，比如我们的`SeaFoodRestaurant`和`ChickenFoodRestaurant`结构。

使用默认值和实现的一致性协议

我们的`SeaFoodRestaurant`结构不需要定义菜单，因为它的默认值取自扩展中声明的默认餐馆值。

display menu func 也是如此，它不是必需的，因为我们使用的是扩展中的默认实现。

使用默认值和实现但覆盖它的一致性协议

现在我们的`ChickenFoodRestaurant`从默认实现和值中获益，但是覆盖了它的`displayMenu`实现。让我们看看调用这两个对象时会发生什么:

一个操场文件使用我们的 func 显示菜单

![](img/a5cd9ae985d6b69e628036e784cff3c1.png)

原木

如我们所见，`ChickenFoodRestaurant`使用其默认实现，而`SeaFoodRestaurant`使用协议默认实现。

使用默认值作为 func 输入参数的协议扩展

我们添加了一个名为`openingHours`的新 func，并且在扩展中，我们给它的输入日期一个默认值(`day = Date()`)。

这些扩展使我们能够向我们的协议 func 输入添加默认值。无论如何，如果你的一致性类覆盖了它的实现，它将不得不声明默认值。

另一方面，我们只在协议扩展中声明了`markFavorite` func。这是向我们的协议添加额外功能的一个很好的方式，作为所有符合我们协议的对象的基类。

# 具有协议扩展和多态性的模糊方法实现

有时，当我们使用扩展和多态时，我们可能会遇到方法实现不明确的问题，因为我们没有明确指出应该使用方法的哪个实现，从而导致编译错误。

在这种情况下，我们必须将 func 的实现添加到一致性类型中。让我们看一个例子:

在扩展上使用默认实现的新协议

如果我们让前面例子中的`SeaFoodRestaurant`符合这个新协议，会发生什么:

![](img/fe09edb7b5aaec7023fb22bf351f25d9.png)

具有协议扩展和多态性的模糊方法实现

我们遇到了一个不明确的实现问题，我们如何解决它？我们的`SeaFoodRestaurant` *必须实现*它的版本`openingHours(day: Date)`。

还有其他令人困惑的情况需要解释，我们已经看到了。当我们谈论协议扩展时，它允许我们添加一些方法的默认实现，也可以添加新的方法实现，但是有什么区别呢？

## **当协议中存在方法声明时**

该协议声明了`openingHours(day: Date)`方法，并提供了一个默认的实现。

该方法在`SeaFoodRestaurant`实现中被覆盖。因此，无论变量的类型如何，都会在运行时调用方法的正确实现。

## **当协议中不存在方法声明时**

因为该方法未在协议中声明，所以该类型无法重写它。这就是为什么被调用方法的实现依赖于变量的类型。

如果变量是`SeaFoodRestaurant`类型的，则调用该类型的方法实现。如果变量是类型`Restaurant`，则调用协议扩展的方法实现。

# 方法分派，协议中的动态与静态

方法分派是在调用方法时选择执行什么实现的方式。当编译器在编译时解析为静态分派时。当它在运行时被解析时，它是动态分派的。

简而言之，引用类型使用动态调度，在运行时从继承层次结构中选择实现。值类型使用静态分派，因为它们不需要继承。

所有需要的协议方法都使用动态调度，因为编译器必须寻找该方法的具体实现。

有一些例外，例如，当你创建一个没有类型的变量，但它只符合协议，它总是在使用静态调度的存在容器中创建。

协议碎片

**sambosTavern** 使用静态调度，而**bubbagmp**使用动态调度。

# Swift 标准库扩展

除了扩展您自己的协议，您还可以从 [Swift 标准库](https://developer.apple.com/documentation/swift/swift_standard_library)扩展协议。我们来看一个小例子。

你还记得我们举的鸡肉菜单和切片的例子吗？又来了:

显示菜单和菜单

让我们看看如何改变它，扩展 Swift 标准库:

扩展 Swift 标准库

这是一个很好的方式来获得有用的机制来支持我们自己的协议。

# 协议扩展与基类

正如我们所见，协议扩展和基类可能看起来非常相似，但是使用协议扩展有几个好处。

因为类、结构和枚举可以符合多个协议，所以它们可以采用多个协议的默认实现。这在概念上类似于其他语言中的多重继承。

另一个好处是，协议可以被类、结构和枚举所采用，而基类和继承只能用于类。

# 良好的 API 设计技巧

既然您已经了解了协议的行为方式，那么最好是回顾一下什么时候应该使用协议。最佳使用协议的一些建议:

*   从具体的用例开始，进入协议。
*   考虑从标准库中定义的现有协议组成新协议。参考下面的[苹果文档](https://developer.apple.com/documentation/swift/adopting_common_protocols)可以得到一个很好的例子。
*   首先，探索具有具体类型的用例，理解您想要共享什么代码，并找到它在哪里被重复。然后，用泛型将共享代码分解出来。这可能意味着您需要创建新的协议。发现对通用代码的需求。
*   考虑定义一个泛型类型，而不是泛型协议。

# 协议见证人

在 swift 的以前版本中(<5.3) when we try to use a protocol with enums, Swift restrict the protocol conforming matching model forcing us to use a manual implementation instead use the enum cases with the same name and arguments as the protocol, like this example:

Protocol Witness

Swift 5.3 has lifted that restriction so we can make our enums to conform protocols matching the vars and funcs as enum cases, like this example

Enum Protocol Witness

# Conclusion

Here you have some powerful tools to start using protocols.

There are more benefits and mechanisms like copy on write and conditionals that we didn’t see in this article, but with all the information here, you should be able to go deeper with the use of protocols and maybe this article stimulates your curiosity.

# Bibliography

1.  [协议定义](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html)
2.  [良好的 API 设计](https://developer.apple.com/videos/play/wwdc2019/415/)
3.  [仿制药](https://docs.swift.org/swift-book/LanguageGuide/Generics.html)
4.  [采用通用协议](https://developer.apple.com/documentation/swift/adopting_common_protocols)
5.  [面向协议的编程—乔恩·霍夫曼](https://www.amazon.com/Protocol-Oriented-Programming-Swift-Hoffman/dp/1785882945)
6.  [流行](https://www.tensorflow.org/swift/tutorials/protocol_oriented_generics)