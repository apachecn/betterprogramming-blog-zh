# 在 Flutter 中探索继承的小部件

> 原文：<https://betterprogramming.pub/exploring-inherited-widget-in-flutter-10ab959017cb>

## 通过示例了解如何使用继承的小部件

![](img/f18e907b4a0d2abc1995bbc736cd5870.png)

詹姆斯·哈里森在 [Unsplash](https://unsplash.com/) 上拍摄的照片

考虑一个用例，当互联网连接不可用时，您想要更新小部件树中的一些元素。实现这一点的一个可能方法是使用继承的小部件，它解决了您希望从树的任何位置访问数据的用例。

您可能已经不知不觉地在 Flutter 中使用了继承的小部件。因为 Flutter framework 在内部使用它来解决多种用例，例如主题化、设备大小调整、路由等等。

# **但是什么是继承的小部件呢？**

您在一个小部件树中嵌套了多个小部件，并且您试图访问树中某处的数据。

我们可以通过构造函数来传播数据，但这并不麻烦，所以继承的小部件就派上了用场。

如果您的小部件树是继承的小部件的子部件，那么您可以在小部件树中的任何位置访问数据。

我们将一步一步来，看看如何使用继承的小部件？

所以为了使用继承的小部件，你必须在你的类中扩展它。例如:

```
class InheritedNetworkHandler extends InheritedWidget { }
```

但是只要你扩展`InheritedWidget`，编译器就会抱怨——“缺少`InheritedWidget.updateShouldNotify` *的具体实现”。*如果你跳到继承的小部件的定义(`cmd + click`，你会看到它被定义为一个`Abstract` 类，有两个方法:-

*   `createElement`，这是一个被覆盖的方法。
*   `updateShouldNotify`，未实现的方法。

我们将研究这种方法，让我们修复错误，-

```
class InheritedNetworkHandler extends InheritedWidget { @override
  bool updateShouldNotify(InheritedWidget oldWidget) {
    return true;
  }
}
```

**返回类型** :
返回值决定了当继承的小部件中的数据发生变化时，Flutter 是否应该重新绘制小部件。

> 注意:-在重绘的情况下，它只会重绘使用继承的小部件的小部件。

**参数** :
但是参数名`OldWidget`是什么意思呢？
`OldWidget`是一个继承的小部件，在用新部件重建之前，使用旧数据。

我在前面几行提到的数据是什么？

在我们到达那里之前，你可能还会看到编译器在报错。我们将遵从编译器的建议，并且不要忘记将子参数传递给超类，因为它是一个必需的参数。子树将使用继承的小部件的数据。

更新后的代码如下所示:

```
class InheritedNetworkHandler extends InheritedWidget { const InheritedNetworkHandler({required Widget child}) :      super(child: child); @override
  bool updateShouldNotify(covariant InheritedWidget oldWidget) {          return true;
  }
}
```

回到数据，它是一个在继承的小部件中定义的字段。可以在整个子小部件树中访问该字段。我们将向继承的小部件添加一个字段:

```
class InheritedNetworkHandler extends InheritedWidget { // Data hold by inherited widget
  final bool isNetworkAvailable; const InheritedNetworkHandler({required this.isNetworkAvailable, required Widget child}): super(child: child); @override
  bool updateShouldNotify(covariant InheritedWidget oldWidget) { return true;
  }
}
```

所以这里我们定义了`InheritedNetworkHandler` *中的`isNetworkAvailable`属性。*

> 注意:- *isNetworkAvailable* 被标记为 final，因为继承的小部件是不可变的，这意味着这些数据在应用程序的生命周期中不会改变。
> 改变它的唯一方法，是通过重建继承的小部件。

现在我们可以在任何子部件中访问继承部件的值，

```
class ChildWidget extends StatelessWidget {
  const ChildWidget({Key? key}) : super(key: key); @override
  Widget build(BuildContext context) {

    // Something new?
    final bool? isNetworkAvailable =   context.dependOnInheritedWidgetOfExactType<InheritedNetworkHandler>()?.isNetworkAvailable; return Container();
  }
}
```

我们正在使用一种方法`dependOnInheritedWidgetOfExactType`。顾名思义，它试图在小部件树上找到给定类型 T 的具体实现。

但是这对于跨部件使用来说是不是太长了？

是的，它是，我们将简化它。我们将把它作为助手方法添加到`InheritedNetworkHandler`:

```
class InheritedNetworkHandler extends InheritedWidget {
  ... static InheritedNetworkHandler? *of*(BuildContext context) => context.dependOnInheritedWidgetOfExactType<InheritedNetworkHandler>();
}
```

我们定义了一个静态助手方法`of`，它将`BuildContext`作为参数并返回一个`InheritedNetworkHandler`。让我们在子部件中使用它。

```
class ChildWidget extends StatelessWidget {
  const ChildWidget({Key? key}) : super(key: key);@override
  Widget build(BuildContext context) {

    // Updated version
    final bool? isNetworkAvailable = InheritedNetworkHandler.*of*(context)?.isNetworkAvailable;; return Container();
  }
}
```

你以前遇到过`of`方法吗？

*   `MediaQuery.of(context)`
*   `Navigator.of(context)`

看我告诉你你已经使用了一个继承的小部件，所以如果你跳到`of` 方法的定义，它也使用`dependOnInheritedWidgetOfExactType` *。现在每次你使用`.of(context)` 你就知道它是干什么的了。*

我们将这些片段连接在一起，并看到完整的代码。

为了这个实现，我使用了 [connectivity_plus](https://pub.dev/packages/connectivity_plus) 插件。
还将把我们的`InheritedWidget`包装在一个有状态的小部件上，这样这个小部件的任何状态变化都会重建`InheritedWidget`。

这是继承的小部件的实现，现在我们来看看它的用法:

> 您可以在定义继承的小部件的上下文中访问它。

在上面的例子中，上下文只能由`ChildWidget`子树使用。
如果您导航到新路线，您将无法访问继承的小工具，因为上下文发生了变化。

如果您想在整个应用程序中访问继承的小部件，用继承的小部件包装`MaterialWidget`(上面代码片段中的选项 2)。

如果您想知道当我在继承的小部件中更新数据时是否需要重新构建整个树，答案是否定的。

Flutter 执行某些优化，它只重建使用继承的小部件中的数据的小部件。

# 资源

从 Flutter 团队继承的小部件的一些有用资源，

*   [继承的小工具解释](https://www.youtube.com/watch?v=Zbm3hjPjQMk)
*   [继承的小部件(本周小部件)](https://www.youtube.com/watch?v=1t-8rBCGBYw)
*   [使用继承的小部件管理应用程序状态](https://www.youtube.com/watch?v=LFcGPS6cGrY)