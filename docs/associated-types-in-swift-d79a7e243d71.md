# 在 Swift 中创建具有相关类型的通用协议

> 原文：<https://betterprogramming.pub/associated-types-in-swift-d79a7e243d71>

## 如何使协议通用化以及这样做的好处

![](img/3799519e15704e1183924c61ba63bbbe.png)

达莉亚·内布里亚希娜

在讨论 Swift 中的[泛型时，我们学习了如何使结构、枚举和类成为泛型。](https://medium.com/@navdeepsingh_2336/generics-in-swift-13e792249cad)

我们发现了如何在编译时传递抽象的类型信息，让编译器将这些信息从编译时传递到运行时。我们使用协议来声明这些类型的能力。

但是协议呢？我们如何使协议通用，这样做的好处是什么？

在本文中，我们将讨论通用协议或相关类型。

# 定义通用协议

协议可能给你的印象是某种已经通用的东西，因为任何类型都可以符合协议，但是协议的内部是什么呢？

我们能想出一个内部通用的协议吗？

这个问题的简短回答是，这是可能的，但是实现与我们讨论的其他泛型类型有点不同。第一步是使用协议定义一个接口，如下面的代码所示:

```
protocol Stack {}
```

根据定义，`Stack`只是一个功能有限的数组。一般来说，`Stack`可以对它持有的元素执行以下功能:

*   `Push`一个元素到了`Stack`的顶端。
*   `Pop`除去顶端的元素。
*   `Peek`在顶部元件上，不从`Stack`上移除。

`Stack`通常在我们希望保持后进先出(LIFO)顺序时使用，即当从`Stack`弹出时，我们将最后一个元素推送到`Stack`。

现在我们已经了解了这些函数，让我们用代码来定义接口:

由于 Swift 更倾向于值类型，假设我们使用数组创建具体的`Stack`类型作为结构，我们需要在协议中标记方法变异，正如你在上面的代码中看到的。

你可能已经注意到在我们的`protocol`中使用了一个特殊的参数`Element`。这是因为我们希望该协议的任何具体类型实现都能够符合该协议并工作，尽管它包含`String`、`Int`、`Float`等。

最终目标是确保`Stack`的每个具体实现只有一种类型。

在我们的协议中声明`Element`类型时，您可能也注意到了关键字`associatedtype`。

关联类型的工作方式类似于类型参数，并提供一个占位符名称，作为协议的一部分。

在我们遵照协议创建具体的实现之前，不需要实际的类型。

## 关联类型

通过使用一种叫做*关联类型*的特殊类型，协议可以变得通用。

关联的类型可以在带有关键字`associatedtype` 约束的协议中声明，然后在泛型类型中使用`typealias`来替换这个`associatedtype`、或者我们可以让编译器推断类型。

# 实现通用协议

在上一节中，我们讨论了如何使用`associatedtype`定义通用协议。在本节中，我们将遵循协议并创建一个具体的类型。

有两种方法可以做到这一点。第一种方法是明确说明`Element`类型。让我们从创建一个具体类型`StringStack`开始，如下所示:

```
struct StringStack: Stack { typealias **Element** = **String**}
```

在上面的代码中，我们通过使用`typealias`关键字将`Element`显式定义为具体类型(在本例中为`String`)来满足`associatedtype`需求。

这告诉编译器在通用协议中定义的所有方法现在将使用`String`而不是通用`Element`类型。

`StringStack`的进一步实施现在可以如下:

```
**struct** StringStack: **Stack** { **typealias** Element = **String** **private** **var** stringArray: **Array**<**String**> **mutating** **func** push(**_** element: **String**) { stringArray.append(element) } **mutating** **func** pop() -> **String**? { **return** stringArray.popLast() } **var** peek: **String**? { **return** stringArray.last }}
```

第二种方法是让编译器推断出`associatedtype`需求的类型。

由于我们已经在上面的代码中为两个函数和 computed 属性明确指定了类型参数，这应该足以让编译器推断出类型，因此，我们不再需要`typealias`关键字。

因此，即使我们删除了`typealias`声明，一切都应该像预期的那样工作，代码将如下所示:

这就是我们如何给出协议的一般要求。它非常简单，但可以在许多用例中使用。

由于我们现在知道了通用协议以及如何使用它，我们现在可以将我们的知识扩展到更复杂的概念，比如通用协议的类型约束。我们将在接下来的文章中讨论所有这些以及更多内容。

感谢阅读。