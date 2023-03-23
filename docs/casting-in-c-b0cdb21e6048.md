# C#中的造型——以正确的方式进行类型转换

> 原文：<https://betterprogramming.pub/casting-in-c-b0cdb21e6048>

## 类型转换是 C#编程语言的核心部分，它在处理类型时提供了灵活性

![](img/738da3d3ae50b195f5419a24cd13d8d6.png)

[来源](https://unsplash.com/photos/Z4Sxy1_3wdY)

[](https://dleroari.medium.com/membership) [## 通过我的推荐链接加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

dleroari.medium.com](https://dleroari.medium.com/membership) 

让我们停下来想一想，我们正在给某人传球。收到它的人不知道它是篮球、网球还是足球。这意味着他们不得不猜测，或者我们需要一种方法来通知这个人，以便他们做出正确的反应。

幸运的是，在现实世界中，我们知道这些事情，因为我们可以直观地看到它，但在编程中，这些事情必须由编译器以某种方式强制执行。

编程中的数据类型是一种告诉接收者预期值的方式，有时值是相同的类型，其他时候我们想要改变它们。一种非常重视数据类型的著名编程语言是编程语言 [C#](https://docs.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/) 。

在 C#中，这是一种编译时静态类型的语言，一旦变量被声明，类型就不能改变，除非它是[隐式可转换的](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/numeric-conversions#implicit-numeric-conversions)。

例如，如果我们试图将一个类型为`double`的变量赋给一个`int`，编译器会输出一个隐式类型错误，因为我们失去了上下文(小数)。

这些东西有助于我们进行安全的类型转换，防止我们处理损坏的数据，否则，我们最终会得到错误的数据和不满意的客户。

这是一个相当短的指南，涵盖了 C#中的强制转换，以帮助任何想了解它如何工作以及何时使用它的人。

本质上，当我们谈论造型时，我们的意思是将变量的类型从 A 改为 b。例如，如果一个变量的类型是`int`，我们想将其改为`double`，这个过程称为造型或类型转换。

# 我们要解决的问题

1.  隐式铸造
2.  显式造型
3.  `is`操作员
4.  `as`操作员

# 1.隐式铸造

隐式转换是指我们在不丢失任何数据的情况下进行类型转换，这也被称为*隐式转换*。这意味着编译器会自动为我们处理转换。

这里有一个很好的[列表](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/numeric-conversions#implicit-numeric-conversions)，它显示了编译器从左向右读取时哪些数据类型可以隐式转换。

如果在此过程中丢失了一些信息，编译器会用隐式类型错误通知您，以确保数据没有损坏。下面是一个编译器成功进行隐式转换的例子:

```
// Implicit conversion
int price = 27;
double totalPrice = price; (success)
```

如上所示，当进行从类型`int`到`double`的转换时，值 27 保持不变。但是如果我们得到不同的结果，比如 26 而不是 27，那么我们就丢失了原始数据。

现在，让我们看看当有一个相反的隐式转换时会发生什么，从类型`double`到`int`，这一次有一些额外的小数:

```
// Implicit conversion
double price = 49.55; 
int totalPrice = price; // Implicit conversion errorOutput: Error CS0266 Cannot implicitly convert type ‘double’ to ‘int’. An explicit conversion exists (are you missing a cast?)
```

下面是一个例子，编译器发现当前值已被更改，并通过显示隐式转换错误来标记它。为了理解为什么我们会得到一个错误，我们需要更仔细地看看数据类型支持什么。

`int`类型不支持小数(逗号后的值)，但是`double`类型支持，因此，我们最终会丢失小数`.55`。所有这些都是因为编译器足够聪明，能够意识到我们正在处理损坏的数据。

但是如果我们意识到小数丢失了，并且需要一种方法来通知编译器继续处理，那该怎么办呢？先说显式选角！

# 2.显式造型

显式强制转换是指当你进行类型转换时，你接受使用[强制转换操作符](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-operator-) `[()](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-operator-)`会丢失数据。它建立在与隐式转换相同的概念上，除了不管数据丢失它都继续，因为它看到开发人员熟悉它。

让我们来看一个转换示例，其中我们丢失了一些数据:

```
// Explicit conversion
double price = 49.55; 
int totalPrice = (int)price; // output: 49
```

如上所示，唯一的区别是我们添加了带有我们想要转换的类型的[转换操作符](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-operator-) `[()](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-operator-)`，比如`(int)price`，这是一个显式转换的例子。

为了正确理解，我们说我们想要获取`price`变量，并将其转换(转换)为类型`int`(从右向左读取)。一旦我们执行了代码，编译器就会检测到它并执行类型转换。

最终结果是我们丢失了小数，但是由于我们正在进行显式转换，编译器理解我们被告知了结果，并且没有显示任何隐式转换错误。

# 3.`is`操作员

`is`操作符用于在进行安全的类型转换之前进行类型检查，以减少类型错误。

有时我们需要确保两个对象、变量或类之间的类型是相同的，以避免编译时出错。如果两个类型相等，则返回`true`，如果不相等，则返回`false`。

例如，假设我们有两个类，一个基类(`User`)和一个派生类(`Student`)，我们想使用`is`操作符执行类型转换:

```
public class User
{
    public string Id = "1234567";
}

public class Student : User { }
```

现在我们想从 student 类访问 user 类中的一个字段，比如检索`id`，为此，我们首先需要检查类型是否相等(`true`，然后执行类型转换。

```
var student = new Student();

if (student is User) // true
{
    var stud = (User) student; 
    Console.WriteLine(stud.Id); // output: 1234567
}
```

如上所示，第一条语句`student is User`返回`true`，因为学生类继承了用户类`public class Student : User {}`。

如果我们尝试反过来，检查基类是否是派生类的类型，它返回`false`。这是一个正常的初学者错误，经验法则是我们只能将派生类与其继承的类进行比较，换句话说，就是基类。

`is`操作符很棒并且易于阅读(声明性的),但是如果我们想用一行代码完成上面显示的所有事情呢？先说一下`as`运算符。

# 4.作为操作员

除了在一个地方为我们执行类型转换之外，`as`操作符几乎与`is`操作符相同。

如果转换失败，`as`操作符将总是返回`null`，这意味着`as`操作符从不抛出异常，这在某些情况下可能是好事也可能是坏事，这取决于项目需求。

让我们使用前面显示的使用`as`操作符的同一个例子:

```
var stud = new Student() as User;
Console.WriteLine(stud.Id); // output: 1234567
```

如您所见，我们在一行中执行了整个类型转换，这在可读性方面非常好，尤其是对于像我这样懒惰的开发人员。

然而，我们仍然需要确保如果`stud`实例是`null`，我们不会访问任何字段。这意味着我们需要一个额外的条件语句来保证这种安全性。

# 摘要

好了，现在我们已经学习了 C#中多种类型转换的方法，以及哪一种用于正确的工作。以下是对每一个问题的快速回顾:

*   隐式转换是编译器自动为我们执行转换，只要我们不丢失任何数据。
*   显式强制转换是开发人员手动确定要转换成什么类型。
*   在进行类型转换之前，我们希望在`is`操作符中检查两个值之间的类型。它返回`true`或`false`。
*   在进行类型转换之前，我们希望在`as`操作符中检查两个值之间的类型。它返回结果(转换后的值)或`false`。

> 如果你喜欢这篇文章，并想要更多这样的，请鼓掌❤，并与可能需要它的朋友分享，这是好的因果报应。
> 
> 你可以在我每周发表文章的媒体上找到我。或者你可以在推特上关注我。