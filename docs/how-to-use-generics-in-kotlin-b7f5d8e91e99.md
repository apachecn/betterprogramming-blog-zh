# 如何在 Kotlin 中使用泛型

> 原文：<https://betterprogramming.pub/how-to-use-generics-in-kotlin-b7f5d8e91e99>

## 让你的代码最小化

![](img/0564c582a1594ff170f3690b2de1fbbb.png)

马克·赖歇尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为开发人员，我们的主要职责之一就是尽可能地减少代码，避免重复代码。泛型是提供更好的性能、更少的代码、可重用的代码等等的功能之一。所以我相信每个开发者都应该有关于泛型的知识。

# 为什么我们需要泛型？

正如我们看到的，`SearchUtil`类将接受一个整数列表作为参数。`searchItem`函数将一个整数(需要搜索的)作为输入并返回结果。这似乎很好。不是吗？。那还有什么问题？

假设，现在你需要搜索一个字符串或对象的列表。你会怎么做？这个问题有两种可能的解决方案。

1.  创建另一个`SearchUtil`类和`searchItem`函数，它们将接受字符串或对象类型并返回结果。
2.  使用泛型

解决方案 1 显然不是一个好主意。因为我们要写很多代码，而且很多代码会重复。在这种情况下，泛型可以拯救我们。

# 什么是泛型？

根据官方文件，

> 泛型是类、结构、接口和方法，它们为自己存储或使用的一个或多个类型提供了占位符(类型参数)。泛型集合类可能使用类型参数作为其存储的对象类型的占位符；类型参数显示为其字段的类型和其方法的参数类型。泛型方法可能使用其类型参数作为其返回值的类型，或者作为其某个形参的类型。

简而言之，

> 泛型是一个灵活的系统，允许您处理任何数据类型。它把类型的负担从你身上转移到了编译器身上。

我觉得这是最简单的解释。

# 行动中的仿制药

现在我们将把上面的例子转换成泛型。要将`SearchUtil`类转换为泛型，我们需要如下修改它

```
class SearchUtil*<*T*>(*private val list: List*<*T*>)*
```

这里的`T`表示任何一种类型都可以用作参数。

还有，我们需要修改`searchItem`功能。

```
fun searchItem*(*element: T, foundItem: *(*element: T?*)* -> Unit*)*
```

现在我们可以使用任何我们想要的类型。

```
val searchUtil = SearchUtil*(*list = listOfObjects*)* val searchUtil = SearchUtil*(*list = listOfNumber*)*
```

上面的代码将没有任何问题。我们来看一个真实的例子。

这里我们创建了一个`Person`数据类。我们正在对`listOfPerson`执行搜索操作。现在，如果我们想在一个整数列表上执行搜索操作，我们可以在不改变`SearchUtil`或`searchItem`的情况下完成。

```
val listOfNumbers = *listOf(*25, 46, 35*)* val searchUtil = SearchUtil*(*list = listOfNumbers*)* searchUtil.searchItem*(*element = 25*)* **{** *println(*"Search result : $**it**"*)* **}**
```

太棒了，不是吗？。

今天到此为止。我希望你学到了新的东西。如果你有什么建议，请在评论中分享。直到我们再次见面…干杯！

```
**Want to Connect?**If you want to, you can connect with me on [Twitter](https://twitter.com/FarhanT99598254) or [LinkedIn](https://www.linkedin.com/in/farhan-tanvir-b08520151/).
```