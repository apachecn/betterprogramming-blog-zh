# 理解接口和抽象类的使用

> 原文：<https://betterprogramming.pub/understanding-use-of-interface-and-abstract-class-9a82f5f15837>

## 何时以及如何实现抽象和接口

![](img/1cdb85e4a18230fe3928aac7ef2614af.png)

照片由[梅林达·金佩尔](https://unsplash.com/@melindagimpel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/newspaper-calculator?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# **接口类**

我们知道接口是由关键字`interface`定义的，所有的方法都是抽象的。接口中声明的所有方法都必须是公共的；这就是接口的本质。

这里有一个例子:

在接口中，没有定义方法体，只有名称和参数。如果我们不使用接口，会遇到什么问题？为什么要用`interface`？

你会在这篇文章中找到这些问题的答案。请参见下面的代码:

在上面的例子中，我没有使用`interface`。我使用`LogToFile`类写入日志。

但是，如果我想用`LogToDatabase`写一个日志，我必须在上面代码的第 23 行修改硬编码的类引用。

那行代码是:

```
public function __construct(LogToFile $logger)
```

该代码将是:

```
public function __construct(LogToDatabase $logger)
```

在一个大型项目中，如果我有多个类，并且我需要更改它们，我必须手动更改所有的类。

但是，如果我们使用一个接口，这个问题就解决了；我们将不需要手动更改代码。

现在，看下面的代码，试着理解如果我使用`interface`会发生什么:

现在，如果我从`LogToDatabase`更改为`LogToFile`，我不必手动更改构造函数方法。在构造函数方法中，我注入了一个`interface`；不是任意类。

如果您有多个类，并且从一个类交换到另一个类，您将获得相同的结果，而不会改变任何类引用。

在上面的例子中，我用`LogToDatabase`写日志，现在我想用`LogToFile`写日志。

我可以这样称呼它:

```
$controller = new UsersController(new LogToFile);
$controller->show();
```

我不用改变其他类就能得到结果，因为`interface`处理了这个交换问题。

# **抽象类**

抽象类是只由程序员部分实现的类。它可能包含至少一个`abstract`方法，这是一个没有任何实际代码的方法——只有名称和参数，并且被标记为“抽象”。

抽象方法只是一个函数定义，用来告诉程序员该方法必须在子类中实现。

这里有一个例子:

现在，问题是:“我们如何知道一个方法将被需要，并且它必须被实现？”我将在这里尝试解释这一点。

请看`Tea`课:

现在，我们来看看`Coffee`类。

在上面的两个类中，三个方法`addHotWater()`、`addSugar()`和`addMilk()`是相同的。

所以，我们应该删除重复的代码。我们可以这样做:

我创建了一个`abstract`类，并将其命名为`Template`。

这里我定义了`addHotWater()`、`addSugar()`、`addMilk()`，做了一个抽象方法命名为`addPrimaryToppings`。

现在，如果我让`Tea`类扩展`Template`类，我将得到三个已定义的方法，并且我必须定义`addPrimaryToppings()`方法。以类似的方式，`Coffee`类也是如此。

*如果你想练习并获得关于* `interface`和`abstract`职业*的代码，请查看我的*[*GitHub*](https://github.com/nahidulhasan/oop)*(星星总赞赏)资源库。*

感谢阅读。