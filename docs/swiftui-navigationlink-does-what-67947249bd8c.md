# SwiftUI NavigationLink 的奇特之处

> 原文：<https://betterprogramming.pub/swiftui-navigationlink-does-what-67947249bd8c>

## 大多数人对 NavigationLink 和 SwiftUI 本身的第一个问题

![](img/941e90325b9c1d032d780ff7d12ce95e.png)

由 [Safar Safarov](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我非常喜欢 SwiftUI。我喜欢写它。我喜欢学习它。我喜欢读关于它的文章。我喜欢揣摩它的内部和行为。

基本上，我是一个 SwiftUI 极客。

因此，我在读一篇文章，其中一位读者评论说，当他们试图在自己的应用程序中实现建议的解决方案时，他们看到了一些意想不到的行为。

什么行为？很高兴你问了。

# 了解导航链接

它使用`NavigationLink`处理。查看以下代码:

这是一个非常简化的 iDine 应用程序的主菜单屏幕，我在几篇文章中用它来说明各种 SwiftUI 特性。

基本上，我们在`NavigationView`内部有一个`List`。使用`ForEach`，我们在菜单上显示每个项目的名称。该名称被包装在一个`NavigationLink`中，其目的地是一个用于显示该商品的`DetailView`。

足够简单和基本的 SwiftUI。

但是当我们显示菜单时会发生什么呢？还有什么意想不到的行为？

# 菜单上有什么？

为了找到答案，让我们添加一个带有 print 语句的初始化器到我们的`DetailView`中，这样我们就可以看到视图是何时构造的。

```
struct DetailView: View { let item: MenuItem

    init(item: MenuItem) {
        self.item = item
        print("DetailView Initializing \(item.name)")
    } var body: some View {
        ScrollView(.vertical) {
            VStack {
                Image(item.mainImage)
                ...
```

现在让我们运行我们的主菜单屏幕并检查日志。

```
DetailView Initializing Maple French Toast
DetailView Initializing Stack-o-Pancakes
DetailView Initializing Power Muesli
DetailView Initializing Fresh-baked Croissant
DetailView Initializing Full English
DetailView Initializing Porridge Deluxe
DetailView Initializing Penne Carbonara
DetailView Initializing Mushroom Tagliatelle
DetailView Initializing Tower Burger
DetailView Initializing Thai Red Curry
DetailView Initializing Paella Alicante
DetailView Initializing Superfood Salad
DetailView Initializing Pesto Farfalle
DetailView Initializing Corn on the cob
DetailView Initializing Fillet Steak
DetailView Initializing Cheese Toastie
DetailView Initializing Pepperoni Pizza
```

嘣。

对某些人来说，这种行为完全出乎意料。我是说，我们没有打开任何导航链接。那么，为什么我们的目的地视图的所有*都被创建和初始化呢？*

# 视图是结构

我已经在几篇文章中讨论过这个问题，最近的一篇是在 SwiftUI 中的[深入内部视图、状态和性能，但它值得重复:](https://medium.com/p/deep-inside-views-state-and-performance-in-swiftui-d23a3a44b79)

> **SwiftUI 视图不是视图。它们是视图的定义。**

当 SwiftUI 想要呈现一个视图时，它调用它的`body`变量来获得我们对这个特定视图应该如何出现的定义。这些定义只不过是 Swift 结构。

主菜单视图根处的`NavigationView`实际上只是一个结构体，它希望初始化器中的一些参数告诉它包含什么内容。

```
struct NavigationView<Content> : View where Content : View {
    init(@ViewBuilder content: () -> Content) 
}
```

这里的参数是一个`ViewBuilder`，一个被调用时将提供视图内容的函数。在上述菜单视图的情况下，该内容将评估为一个`List`。

`ViewBuilder`背后的实际机制超出了本文的范围。这里重要的是，我们需要初始化我们的`NavigationView`，为了做到这一点，Swift 编译器将需要首先评估该视图的内容，即列表。但是 list 只是另一个需要自己参数的结构，这意味着在之前的*Swift 可以提供它需要的 list 来评估 list 的*内容*。*

这种递归求值一直发生，直到我们到达列表中的`ForEach`结构。当 SwiftUI 呈现列表时，它基本上是用作为数据源的`ForEach`来呈现一个`TableView`。

因此，当需要呈现给定的单元格时，SwiftUI 会要求`ForEach`数据源给它一个描述单元格内容的视图。这最终将我们引向`NavigationLink`，这个特殊故事的罪魁祸首。

# 导航链接

让我们看一下`NavigationLink`的相关初始化器的一个稍微简化的版本。

```
struct NavigationLink<Label, Destination> : View {
    init(destination: Destination, @ViewBuilder label: () -> Label)
}
```

`NavigationLink`是一个结构，在这种情况下，它需要一个定义其目标视图的参数，并再次使用一个`ViewBuilder`来定义单元格本身的内容。

这是细则。

*   为了构造一个`NavigationLink`结构，我们必须给它的初始化器所有它需要的参数。
*   其中一个参数是目的地视图。
*   那个“视图”反过来也只不过是一个结构。在之前必须完全构造和初始化*的值类型可以作为参数提供给`NavigationLink`。*

# 找到了。

这就是为什么我们从`DetailView`的初始化器中看到所有的打印语句。

> **每个详细视图结构必须完全构建并完全初始化，以便作为参数传递给** `**NavigationLink**` **。**

当你停下来想一想，它真的不能以任何其他方式工作。SwiftUI 可能看起来很神奇，但当你真正了解它时，它只是 Swift 代码，与该语言中的其他东西一样受到相同的规则和限制。

函数接受参数。这些参数在被传递之前必须被初始化——仅此而已。

# 结果

不完全是。因为有一个少校，*少校，*少校的分支。

> **不要在你的视图初始化器中做任何繁重的工作。**

我说的举重是什么意思？

*   不要在初始化器中创建基于类的对象。
*   不要在初始化器中进行 API 调用。
*   不要在初始化器中做大量耗时的处理。

SwiftUI 视图是轻量级结构是有原因的。不要用堆分配来增加视图初始化过程的负担，或者在那个时间点进行大量的处理。

此外，您并不真正知道 SwiftUI 何时需要访问您的视图定义。或者多久会这样。

所以，如果你不知道*什么时候*一个代码块将被调用，如果你不知道*经常*它将被调用，你应该把关键的或者时间密集型的代码放在那里吗？尤其是这样做会对应用程序的性能产生重大影响的时候？我不这么认为。

# UIKit 和 UIViewController

部分困惑源于在 UIKit 工作。当我们从一个视图控制器切换到下一个视图控制器时，目标视图控制器是在那个时间点构建的。

当我们点击一个按钮来显示一个新的视图控制器时，我们构造这个视图控制器并把它传递给导航控制器的`present`函数。

在这两种情况下，直到触发导航的动作发生后的*才构建目的地。*

但是，正如我们已经看到的，`NavigationLink`不是那样工作的。

它*可能*有。Apple *可能*已经使用了 autoclosure 来捕获目的地视图，并且只有当导航动作发生时才评估闭包工厂。

但是他们没有，所以我们被当前的行为所困。

只要记住。SwiftUI 不是 UIKit。

# 完成块

这就是为什么我们的读者会看到“意想不到”的行为。他对 SwiftUI 如何工作的心理模型与 SwiftUI 在幕后的实际工作方式不符。

同样，如果您还没有这样做，我强烈建议您阅读我之前的文章，[SwiftUI 中的深层视图、状态和性能](https://medium.com/p/deep-inside-views-state-and-performance-in-swiftui-d23a3a44b79)，在那里我将更详细地讨论这一点和其他 swift ui 行为。

下次见。