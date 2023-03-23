# Python 中的枚举

> 原文：<https://betterprogramming.pub/enumerations-in-python-b01a1fb479de>

## 你应该知道的关于 enums 的一切

![](img/857005e169704bca5d2ae7a051951621.png)

照片由 [Pratiksha Mohanty](https://unsplash.com/@pratiksha_mohanty?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 介绍

在进入所有 Pythonic 的东西之前，让我们从这个问题开始，“什么是枚举”这样，当你在任何编程语言中遇到问题或任务时，你会更好地理解使用枚举，也就是*枚举*(编程语言)，是否是解决问题的好方法。

现在，进入问题:枚举是一个一个地提到几个事物的动作。它强调了这样一个事实，即事物正在被一次一个地特别识别和列出。

现在我们知道了什么是枚举，接下来的问题是:在 Python 这样的编程语言中，它的必要性是什么？首先，当我们在编译时知道所有可能的值时，比如一周中的几天，我们可以使用枚举概念。

以我的专业观点来看，使用枚举来组织常量或一组值比用常量来组织代码更好。

# 如何创建枚举

让我们从头开始。第一步是学习如何创建一个`Enum`类。为了揭示这一点，我们应该知道它是从哪个模块导入的。答案是`Enum`。在 enum 包中，我们有`Enum`类，我们可以从这个类派生出新的枚举。

枚举是通过类语法创建的，这使得开发人员很容易实现。为了清楚地了解如何创建您的定制`Enum`类，请看下面的内容:

简单枚举类

这就像创建一个常规类并声明常量一样简单。但是当我们与`Enum` s 一起工作时，为了更好地理解代码，我们应该遵循一些指导方针。以下是几个重要的例子:

*   通常，`Enum`用于声明常量，所以建议对`Enum`成员使用大写名称。
*   不允许用空格命名`Enum`成员。

# 如何访问枚举值

`Enum`类有人类可读的字符串表示。我们可以通过使用类名和点作为扩展名来访问成员。看一看:

为了访问更多关于`Enum`成员的信息，我们必须用`repr`函数包装它。看一看:

我们还可以使用`dynamicClassAttributes` ——名称和值来单独访问成员的名称和值。看一看:

`Enum` s 支持迭代，所以我们可以创建一个循环并通过索引访问值。看一看:

# 复制枚举成员和值

我们不能用相同的名字声明两个`Enum`成员，但是我们可以将相同的值赋给多个`Enum`成员。在内部，它将使第二个`Enum`成员与第一个`Enum`成员的别名具有相同的值。看一看:

## 独一无二的

相反，如果您只想要每个`Enum`类成员的唯一值，您应该使用唯一的注释。当您应用一个唯一的注释并试图拥有重复的值时，它将抛出一个运行时错误，如下所示:

# 自动生成的值

通常，在 Java 和 Kotlin 这样的语言中，当声明`Enum`成员时，value 字段是可选的。我们可以使用`auto`在 Python 中实现这一点。

通过使用`auto`，实例被替换为`Enum`类套件中的适当值。

它将生成数字并将它们分配给`Enum`成员。让我们来看一下实现:

# 比较

我们可以使用`is`和`is not`操作符通过它们的身份来比较`Enum`类成员。看看下面的代码:

```
Avengers.IRONMAN **is** Avengers.IRONMAN
# OutPut : TrueAvengers.IRONMAN **is** Avengers.THOR
# OutPut : FalseAvengers.BLACK_WIDOW **is not** Avengers.THOR
# OutPut : True
```

我们还可以在成员上使用 type 函数来获取`Enum`类型，并比较它是否属于特定的`Enum`类型。看一看:

目前就这些。希望你学到了有用的东西。感谢阅读！