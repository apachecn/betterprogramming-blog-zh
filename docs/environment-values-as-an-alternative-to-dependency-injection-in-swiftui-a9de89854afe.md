# 环境值作为 SwiftUI 中依赖注入的替代

> 原文：<https://betterprogramming.pub/environment-values-as-an-alternative-to-dependency-injection-in-swiftui-a9de89854afe>

## 利用环境价值观来避免不必要的身体重新评估，使我们的观点更加独立。

![](img/6e0bfb5c7db831b0c7301fadb2c1c858.png)

[皮特](https://unsplash.com/@pieterpanflute?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

> 依赖注入是一个 5 美分概念的 25 美元术语。詹姆斯·肖尔

依赖注入是一种奇特的说法，即我们将为一个对象提供它需要或依赖的对象来完成它的工作。

在 SwiftUI 中，实现依赖注入的一种非常常见的方式是使用环境对象。通过将一个对象注入到我们的视图层次结构中，我们可以将逻辑和数据从视图中抽象出来并放入类中，从而允许我们遵循单一责任原则，并使我们的代码更具可测试性。

举个例子，假设我们有一个包含几个属性的`UserSettings`类。

为了确保我们的`View`可以访问我们的`UserSettings`对象中的值，我们可以使用`.environmentObject()`修饰符注入它，并使用`@EnvironmentObject`属性包装器读取它。

这可能看起来很熟悉。

在这个例子中，`ContentView`需要在`UserSettings`中找到的两个值，但是我们正在创建一个更深的依赖关系，如果没有完整的`UserSettings`对象，我们的视图**将无法工作**。

这有两个明显的缺点:

1.  首先，每当对象中的任何值改变时，视图的`body`将被重新评估，即使改变的值不是这个特定视图使用的值。在我们的例子中，当`soundsEnabled`改变时，我们的视图的`body`将被重新评估，即使它没有在主体中使用。如果你碰巧使用这个方法来注入你的应用程序的状态，就像我过去所做的那样，这个对象可能包含许多值，这些值将触发你的视图层次结构中不必要的视图评估。
2.  第二，当预览一个依赖于环境对象的视图时，我们需要将环境对象注入到预览中，否则它将无法加载。更糟糕的是，任何将该视图显示为子视图的视图都需要在它们的预览中接收环境对象才能加载，尽管通过查看它们的`body`，这一要求并不明显。

还不如开始在你所有的预告片里注射。

# 自定义环境值

与`EnvironmentObjects`一样，`EnvironmentValues`被注入到视图层次结构中，并传播到所有后代，直到它们被新值替换。它们是像`.foregroundColor()`或`.font()`这样的修改器使用的机制。

最大的区别在于，要定义一个定制的环境值，您需要提供一个默认值**。它需要更多的样板文件，但它有它的优势。**

要创建您自己的环境值，您需要创建一个环境键并扩展`EnvironmentValues`；然后，您可以使用`.environment()`修饰符将值插入到层次结构中。

您需要为每个值创建一个 EnvironmentKey 结构，并将其添加到 EnvironmentValues 类型中。

# 环境价值的价值注入

通过从一个特定的环境值中读取，我们的视图主体只有在该值改变时才会被重新评估。并且因为`EnvironmentValues`总是有一个`defaultValue`，这些视图可以被利用而不需要注入任何东西，这意味着你不需要修改你的预览。

需要注意的重要一点是`@Environment`属性是*只读的*。你可能已经注意到我在我的`SettingsProvider`中注入了`UserSettings`，这是因为任何需要修改值的视图，比如本例中的`SettingsView`，仍然需要读取整个对象。

# 不仅仅是用户设置

到目前为止，我一直使用简单的值作为例子，但当用各种数据填充我们的应用程序的视图时，这种技术会很有用。

假设我们正在构建一个应用程序，该应用程序以不同的方式和子集显示用户的交易。

如果我们正在使用 CoreData，我们可以在每个视图中使用一个带有相关查询的`@FetchRequest`;如果我们从服务器获取数据，我们可以在每个视图上从网络获取所需的事务。

然而，我更喜欢的选择是在层次结构中的一个非常高的级别注入事务，并在任何显示它们的视图中读取该值。

获取数据是留给读者的一个练习，但是一旦获取了事务，我们可以使用`.environment`修饰符将它们注入到环境中。

任何想要显示`userTransactions`数组中的值的视图都可以从环境中读取该值，而不必自己获取任何值。

这使得我们的视图变得简单了许多，并且获取事务的逻辑不仅从`View` `struct`中移除，而且在视图层次结构的不同节点中。

# 结论

通过使用值注入，需要*对这些值进行只读*访问的视图将只在那些特定值改变时被刷新。

因为环境值提供了一个**缺省值**，我们不再需要为使用它们的视图的预览注入依赖关系。

使用环境对象的依赖注入在需要对对象的属性进行写访问的视图中仍然有它的位置。

## 继续阅读

在我的上一篇文章中，我探索了使用`EnvironmentValues`创建一个响应链，允许我们遵循一种“开火并忘记”的方法来行动，以便使我们的观点更加独立。

[](/building-a-responder-chain-using-the-swiftui-view-hierarchy-2a08df23689c) [## 使用 SwiftUI 视图层次结构构建响应者链

### 利用环境价值轻松响应视图层次结构中生成的事件

better 编程. pub](/building-a-responder-chain-using-the-swiftui-view-hierarchy-2a08df23689c)