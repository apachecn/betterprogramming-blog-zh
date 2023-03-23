# Dart 中的 6 种构造函数

> 原文：<https://betterprogramming.pub/6-types-of-constructors-in-dart-e810b6d9f354>

## 我们大多数人都知道什么是构造函数，以及它是如何工作的。让我们仔细看看我们一直使用的六种类型的构造函数

![](img/e2beae0174fb99fadbbd88154bbadd43.png)

由 [Oliver Buchmann](https://unsplash.com/@snxiiy?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

编写干净的代码在每种语言和框架中都很重要，不管你是单独的开发人员还是团队的一员。在这里，我将尽可能干净地向您介绍干净代码原则。好吧！让我们开始吧。

# Dart 语言中的构造函数

构造函数允许我们创建类的不同实例。因为 Flutter 是关于窗口小部件的，所以构建窗口小部件需要构造函数。不管你是什么级别的开发人员，你的代码可能会有很多构造函数，因为你可能有很多小部件。知道何时以及为什么使用每种类型的构造函数是很重要的。

现代语言中有许多构造对象的方法，Dart 语言提供了以下构造函数:

```
Table of Contents

[Default Constructors](#787b)

[Parameterized Constructors](#21d2)

[Named Constructors](#0e6a)

[Factory Constructors](#30c0)

[Redirecting Constructors](#d351)

[Constant Constructors](#5871)
```

默认的、参数化的和命名的构造函数被称为“生成式构造函数”，因为它们纯粹生成一个实例。

# 默认构造函数

默认构造函数或“无参数构造函数”不带任何参数。如果我们没有在类中声明它，Dart 编译器会自动创建它(没有参数)。每当创建类的新实例时，都会调用此构造函数。它不接受任何参数，并将调用超类的构造函数，该构造函数也不接受任何参数。

```
class SettingsIcon extends StatelessWidget {

  // Default constructor
  SettingsIcon() {
    print("Settings Icon is created");
  }

  @override
  Widget build(BuildContext context) {
    return IconButton(icon: Icon(Icons.settings), onPressed: () => {});
  }
}
```

## 我应该什么时候使用它？

例如，如果您想在构造函数中记录一些内容，就可以使用它。但是，我们不应该在构造函数内部做太多。创建一个实例是很昂贵的，增加更多的工作量并不是一个好主意。让您的构造函数只用于类初始化。说实话，我从来不用默认构造函数。编译器会为我生成一个。

# **参数化构造函数**

我们也可以将参数传递给构造函数。这种构造函数称为参数化构造函数。它用于为实例配置变量。我们有时需要一个可以接受一个或多个参数的构造函数。大多数情况下，参数化构造函数用于在实例变量第一次创建时赋予它们自己的值。

```
class SettingsIcon extends StatelessWidget {
  final double iconSize;
  final Color iconColor;
  final VoidCallback callback;
  final String tooltip;

  // Parameterized Constructor
  SettingsIcon(this.callback,
      {Key? key,
      this.iconSize = 18,
      this.iconColor = Colors.white,
      this.tooltip = "Settings"})
      : super(key: key);

  @override
  Widget build(BuildContext context) {
    return IconButton(
        icon: Icon(Icons.settings, color: iconColor),
        iconSize: iconSize,
        tooltip: tooltip,
        onPressed: callback);
  }
}
```

在这里，您可以看到我通过添加参数化的构造函数改进了`SettingsIcon`小部件。Dart 还允许您添加可选参数。在我们的例子中，只需要回调函数；其余的变量是可选的。以下是一些要点:

*   总是从创建变量`final`开始。这样，您可以实现对象的不变性。
*   构造函数中可以有必需的和可选的参数。您甚至可以将可选参数设置为默认值。当创建一个类时，这些是有用的工具。按照物品的预期用途使用它们。
*   在构建小部件时，总是使用`Key`作为可选参数。这通常是一种好的做法。
*   确保变量在构造函数中的声明顺序保持不变。
*   为参数使用更具描述性的名称，因为构造函数也是一种文档形式。

# 命名构造函数

在 Dart 中，不能用同一个名字定义多个构造函数(反正我也不是个粉丝)。因此，Dart 引入了一个新的构造函数类型，我们可以给它命名。

```
class SettingsIcon extends StatelessWidget {
  final double iconSize;
  final Color iconColor;
  final VoidCallback callback;
  final String tooltip;

  // Parameterized Constructor
  SettingsIcon(this.callback,
      {Key? key,
      this.iconSize = 18,
      this.iconColor = Colors.white,
      this.tooltip = "Settings"})
      : super(key: key);

  // Named Constructor
  SettingsIcon.dark(this.callback,
      {Key? key, 
      this.iconSize = 18, 
      this.tooltip = "Settings"})
      : iconColor = Colors.black,
        super(key: key);

  @override
  Widget build(BuildContext context) {
    return IconButton(
        icon: Icon(Icons.settings, color: iconColor),
        iconSize: iconSize,
        tooltip: tooltip,
        onPressed: callback);
  }
}
```

这和以前的类是一样的，但是我添加了一个新命名的构造函数 dark，它生成一个黑色图标而不是白色图标。除了命名部分，语法是相同的。所有其他规则仍然适用。它的用法也相当简单。

```
AppBar(title: 
   Row(children: [
     Text(widget.title),
     SettingsIcon(() => {}, iconColor: Colors.black, tooltip: "Go to Settings Page"),
     SettingsIcon.dark(() => {}, tooltip: "Go to Settings Page")
   ]))
```

Dart 构造函数在设计我们的类时给了我们很大的权力；然而，掌握它们的用法需要时间。我坚信命名构造函数可以提高代码的可读性，但是不应该过度使用。

请看 Flutter 的`ListView` 实现，这是一个很好的例子。有一个命名构造函数和三个参数化构造函数:

*   `ListView.builder`
*   `ListView.separated`
*   `ListView.custom`

参数化的构造函数从一个小部件列表中创建一个可滚动的小部件。然而，您通常使用一个`ListView` 来让同一个小部件具有不同的数据，而`ListView*.builder*`可以实现这一点。

请注意，这是意识形态上的差异，而不是结构上的变化。结果仍然是一个可滚动的小部件列表，但是构建的方式不同了。我的意思是创建一个`ListView.dark`构造函数在这个上下文中没有意义。应该建立一些更基本的东西。

# 工厂构造者

首先，“工厂”一词指的是工厂设计模式，它允许构造函数根据传入的参数返回子类的实例(而不是类的实例)。

工厂构造函数只需返回该类类型的实例或实现其接口的实例。这可能是类的新实例、类的现有实例以及子类的新实例或现有实例。工厂必须使用`return`关键字，并且可以决定返回什么对象。

```
class SettingsIcon extends StatelessWidget {
  final double iconSize;
  final Color iconColor;
  final VoidCallback callback;
  final String tooltip;

  // Parameterized Constructor
  SettingsIcon(this.callback,
      {Key? key,
      this.iconSize = 18,
      this.iconColor = Colors.white,
      this.tooltip = "Settings"})
      : super(key: key);

  // Named Constructor
  SettingsIcon.dark(this.callback,
      {Key? key, this.iconSize = 18, this.tooltip = "Settings"})
      : iconColor = Colors.black,
        super(key: key);

  // Factory Constructor
  factory SettingsIcon.create(VoidCallback callback,
      {double iconSize = 18, String tooltip = "Settings"}) {
    return AppTheme.uiConfiguration == UIConfiguration.dark
        ? SettingsIcon.dark(() {}, iconSize: iconSize, tooltip: tooltip)
        : SettingsIcon(() {}, iconColor: Colors.white, iconSize: iconSize, tooltip: tooltip);
  }

  @override
  Widget build(BuildContext context) {
    return IconButton(
        icon: Icon(Icons.settings, color: iconColor),
        iconSize: iconSize,
        tooltip: tooltip,
        onPressed: callback);
  }
}
```

为了保持一致性，我保留了相同的例子，并添加了一些内容。于是，我添加了一个名为`SettingsIcon.create`的工厂构造函数。它检查一些配置设置，然后根据发现的内容创建一个新的实例。您可以用静态方法实现同样的事情，但是静态方法服务于更一般的目的，而工厂方法必须`return`类的一个实例或者它的一个子类。

工厂方法是隐藏类的用户调用静态方法而不是构造函数这一事实的好方法。如果您将类的实例保存在内存中，但不想每次都创建一个新的实例(或者如果创建一个实例的成本很高)，这可能是工厂方法的一个选项。但是，请记住，您不能从这个构造函数中访问`this`关键字，因为它在`static` 范围内。

# 重定向构造函数

在构造函数的右边，我们可以使用`this` 关键字将一个构造函数重定向到同一个类中的另一个构造函数。

```
 // Parameterized Constructor
  SettingsIcon(this.callback,
      {Key? key,
      this.iconSize = 18,
      this.iconColor = Colors.white,
      this.tooltip = "Settings"})
      : super(key: key);

  // Named Redirecting Constructor
  SettingsIcon.dark(VoidCallback callback,
      {Key? key, double iconSize = 18, String tooltip = "Settings"})
      : this(callback,
            key: key,
            iconColor: Colors.black,
            iconSize: iconSize,
            tooltip: tooltip);
```

*   请记住，重定向构造函数不需要主体来工作。
*   您不能在字段初始化器中使用`this` 作为关键字，您仍然可以使用将被重定向的默认值。

# 常量构造函数

也可以在 Dart 中让**成为常量**构造函数。这是什么意思？

如果您的类表示一个在创建后永远不会改变的对象，则可以使用常量构造函数。您必须确保您的类的所有字段都是最终的。

因为我们在例子中使用了 final 字段，所以我们的构造函数可以是一个常量。我们简单地将`const`添加到我们的构造函数定义中。

```
class SettingsIcon extends StatelessWidget {
  final double iconSize;
  final Color iconColor;
  final VoidCallback callback;
  final String tooltip;

  // Parameterized Constructor
  const SettingsIcon(this.callback,
      {Key? key,
      this.iconSize = 18,
      this.iconColor = Colors.white,
      this.tooltip = "Settings"})
      : super(key: key);
```

使用字段初始值设定项时，必须确保使用常数值。让你的类尽可能难改变是一个好的实践。

# 最终注释

*   避免使用默认构造函数，因为编译器会生成一个。
*   `Key? key` 应该始终是 flutter 小部件的可选参数。
*   尽量让你的构造函数没有代码。Dart 已经通过提供没有主体的字段初始化器和构造器，试图将您推向这种良好的实践。
*   在断言中使用构造函数的右侧。
*   参数化构造函数非常适合你的类的基本函数。根据对象的使用方式使用必需和可选参数。
*   使用命名构造函数来增加可读性，但是不要过度使用它们。
*   使用工厂构造函数隐藏创建逻辑或将昂贵的对象存储在缓存中。
*   不要重复相同的参数结构，而是使用重定向构造函数。
*   总是试着考虑你的类的不变性，让你的构造函数保持不变。

感谢阅读。