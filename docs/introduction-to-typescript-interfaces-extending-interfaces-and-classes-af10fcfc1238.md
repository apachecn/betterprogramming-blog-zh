# 类型脚本接口介绍

> 原文：<https://betterprogramming.pub/introduction-to-typescript-interfaces-extending-interfaces-and-classes-af10fcfc1238>

## 如何扩展接口和编写扩展类的接口

![](img/cff9cd78a4fabc4c6f94d0903190040b.png)

照片由 [Natalia Y](https://unsplash.com/@foxfox?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/letters?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

与普通 JavaScript 相比，TypeScript 的最大优势在于，它通过添加确保程序对象类型安全的特性来扩展 JavaScript 的特性。

它通过检查对象所呈现的值的形状来做到这一点。检查形状称为*鸭式分型*或*结构分型*。接口是在 TypeScript 中扮演命名数据类型角色的一种方式。

它们对于在 TypeScript 程序的代码中定义契约非常有用。

在最后一部分，我们将看看如何定义一个 TypeScript 接口，以及如何向它添加属性。我们还研究了对象文字的额外属性检查和接口的定义类型。

在本文中，我们将研究如何扩展接口和编写扩展类的接口。

# 扩展接口

在 TypeScript 中，接口可以像类一样相互扩展。这让我们可以将一个接口的成员复制到另一个接口，并在如何使用接口方面给予我们更多的灵活性。

我们可以在不同的地方重用常见的实现，我们可以以不同的方式扩展它们，而无需为接口重复代码。

我们可以用关键字`extends`扩展接口。我们可以使用关键字来扩展一个或多个用逗号分隔的接口。例如，我们可以在下面的代码中使用`extends`关键字:

然后，为了实现`Dog`和`Cat`接口，我们还必须实现在`Animal`接口中列出的成员。例如，我们将在下面的代码中实现它们:

正如我们所看到的，我们已经在类实现中添加了父接口和子接口的所有内容。我们还可以扩展多个接口，如下面的代码所示:

从上面的代码中我们可以看到，如果我们像对待`Clock`类一样实现`ClockInterface`，我们就拥有了`Machineinterface`、`ProductInterface`和`ClockInterface`的所有成员。

注意，如果我们在多个接口中有相同的成员名，那么它们也必须有相同的数据类型。例如，如果我们有以下代码:

Typescript 编译器会拒绝它，因为我们在`MachineInterface`中有一个字符串`name`，在`ProductInterface`中有一个数字`name`。

如果我们尝试用 TypeScript 编译器编译上面的代码，我们会得到错误:

```
Interface ‘ClockInterface’ cannot simultaneously extend types ‘MachineInterface’ and ‘ProductInterface’. Named property ‘name’ of types ‘MachineInterface’ and ‘ProductInterface’ are not identical.(2320)
```

# 混合类型

我们可以用类型断言操作符重写由对象推断的类型，这在 TypeScript 中用`as`关键字表示。

这样，我们可以在继续使用接口的同时使用具有动态类型的代码。例如，我们可以编写以下代码:

在上面的代码中，我们在`getPerson`函数中有`person`变量，我们用`Person`类型显式设置它，这样我们就可以将`Person`接口中列出的属性分配给`person`变量。

![](img/e6132d1f77174683fe3e2deeaedadc51.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 接口扩展类

TypeScript 接口可以扩展类。这意味着接口可以继承类的成员，但不能继承它们的实现。在这种情况下，类充当所有声明的成员的接口，而不提供实现。

这意味着当我们用私有或受保护的成员扩展一个类时，接口只能由那个类或它的子类实现。例如，我们可以编写一个扩展类的接口，如下面的代码所示:

在上面的代码中，我们首先创建了类`Animal`，它有一个公共成员`name`和一个私有成员`age`。然后，我们添加了一个`BirdInterface`，它通过添加公共成员`breed`和`color`来扩展`Animal`类。

然后，在扩展了`Animal`类并实现了`BirdInterface`的`Bird`类中，我们拥有了`BirdInterface`的所有成员以及`Animal`类的公共成员。

因为私有成员不能在类外访问，所以我们不能访问`Bird`类中的成员`age`。我们也不能在`Bird`班再增加一个`age`成员。

否则，我们会得到错误:

```
Class ‘Bird’ incorrectly extends base class ‘Animal’. Property ‘age’ is private in type ‘Animal’ but not in type ‘Bird’.(2415)” and “Class ‘Bird’ incorrectly implements interface ‘BirdInterface’. Property ‘age’ is private in type ‘BirdInterface’ but not in type ‘Bird’.(2420)
```

然而，如果我们将`Animal`类中的`age`成员更改为受保护成员，它可以被扩展`Animal`的所有子类访问，那么我们可以在`Bird`类中引用它，如下面的代码所示:

方法也是如此。私有方法不能被定义它的类之外的任何东西访问，也不能被任何子类或接口覆盖。例如，如果我们有以下代码:

然后我们会得到错误:

```
Class ‘Bird’ incorrectly extends base class ‘Animal’. Property ‘age’ is private in type ‘Animal’ but not in type ‘Bird’.(2415)” and “Class ‘Bird’ incorrectly implements interface ‘BirdInterface’. Property ‘age’ is private in type ‘BirdInterface’ but not in type ‘Bird’.(2420)
```

但是，我们可以在子类中重写受保护的方法，如下面的代码所示:

在 TypeScript 中，接口可以像类一样相互扩展。这让我们可以将一个接口的成员复制到另一个接口，并在如何使用接口方面给予我们更多的灵活性。

我们可以在不同的地方重用常见的实现，我们可以以不同的方式扩展它们，而无需为接口重复代码。

此外，TypeScript 接口可以扩展类。这意味着接口可以继承类的成员，但不能继承它们的实现。在这种情况下，类充当所有声明的成员的接口，而不提供实现。

这意味着当我们用私有或受保护的成员扩展一个类时，接口只能由那个类或它的子类实现。