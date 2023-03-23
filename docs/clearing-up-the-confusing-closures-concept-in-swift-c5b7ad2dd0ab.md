# 澄清 Swift 中令人困惑的闭包概念

> 原文：<https://betterprogramming.pub/clearing-up-the-confusing-closures-concept-in-swift-c5b7ad2dd0ab>

## 通过例子理解闭包

![](img/8d3d98f855e424fc151b9d7a4b734e8b.png)

micha Kubalczyk 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

闭包在程序员的日常生活中非常重要。它们表示一些可以赋给变量的代码块。此外，这段代码可以传递给一个函数，并用作一个应该在函数的另一个动作之后执行的动作。

现在看起来可能有点混乱，但是我会试着在这篇文章里把一切都讲清楚。

事不宜迟，让我们看看闭包的语法:

这是闭包最简单的例子。如您所见，我们创建了一个名为`greetings`的常量变量，并将其分配给花括号中的消息。在最后一行，闭包被称为。通过调用`birthday()`来执行。你已经注意到花括号和闭包之间的联系了吗？如果是这样，恭喜你！您刚刚学习了闭包的基本概念。

现在，让我们看看闭包的最完整形式，并一步一步地简化它:

*   `(String) -> ( )` —我们用一个`String`参数指定闭包的类型，并等待它不返回任何内容。`(String) -> ( )`相当于`(String) -> Void`。
*   `(name: String) -> ( )` —我们做了相同的闭包类型规范，除了我们给`name`作为我们要传递的参数。
*   关键字`in`作为参数和闭包体之间的边界。所以在`in`关键字之后，我们可以写闭包的一些动作。
*   在`greetings(“Mike”)`中，我们传递一个`name`的`String`。

输出:

```
Hello, Mike!
```

因为我们说过在第一个例子中指定闭包类型做了两次，所以我们可以很容易地省略其中一个规范:

事实上，我们也可以去掉参数的`name`规范，只使用`$0` 简写！

你有没有注意到我们也没有使用`in`关键字？这是因为我们还没有写任何参数名。这里的`$0`是指`String`参数。

还有几个语法注释的例子:

*   `(String, String) -> ()`:`string`的两个参数不返回任何值。
*   `(Int, Int) -> (Double)`:返回`double`的`int`的两个参数。

# 分类方法

让我们来看一个内置的 Swift 闭包(`sorted`)，它为您排序一些数据:

记住:能够使代码简短是好的，但是让你的代码对其他人来说可读性更好就更好了！

# 关闭类型

在 Swift 中，我们有不同类型的闭包，比如`trailing`、`escaping`、`autoclosure`和`autoclosure escaping`。

## 尾随闭包

当闭包是函数中的最后一个参数时，闭包可以写在函数的括号之外，参数的标签可以省略。这被称为拖尾函数:

## 转义闭包

函数返回后执行时，闭包被调用到`escape`。你只需要在参数类型前声明`@escaping`关键字来指出闭包是允许转义的:

## 自动关闭

`Autoclosure`在功能返回前自动执行*。为了使用它，您需要在参数类型之前声明`@autoclosure`关键字:*

## 自动关闭逃逸

您可能已经理解了，`autoclosure escaping`是两个闭包的共生。如果你想让一个自动闭包被允许从一个函数中转义，使用`@autoclosure`和`@escaping`属性:

# 包扎

闭包非常重要，是 Swift 的基本功能。我希望这篇文章能让您更好地理解它们是什么以及如何使用它们。

对其他相关话题感兴趣？欢迎访问我的其他相关文章:

*   [Swift 5 中使用选项的指南](https://medium.com/cleansoftware/beginners-guide-to-using-optionals-in-swift-5-f40caae4edb7)
*   [揭开 Swift 5 中的结构、类和枚举的神秘面纱](https://levelup.gitconnected.com/demystifying-struct-class-and-enum-in-swift-5-43dde089e96a)

如果你有任何批评、问题或建议，欢迎在下面的评论区发表！

感谢阅读。