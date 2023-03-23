# Swift 任务组中处理错误的两种常用方法

> 原文：<https://betterprogramming.pub/2-common-ways-to-handle-errors-in-swift-task-groups-4bc9f231e2eb>

## 任务组中的错误处理

![](img/5d57ada3a719bcfb8b830d4ea6a8abc5.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果您已经阅读了我的[上一篇文章](/understanding-swift-5-5-task-groups-f49fe7c5f873)，那么您现在应该知道如何创建一个任务组，如何向任务组添加子任务，以及如何从所有子任务中收集结果。然而，有一个与任务组相关的重要主题我还没有谈到，那就是“错误处理”。

众所周知，任务组由多个并发运行的子任务组成。当其中一个子任务遇到错误时，任务组应该如何处理该错误？那些还在运行的子任务会怎么样？

在本文中，我们将探讨在任务组中处理错误的两种最常见的方法:

1.  使用抛出任务组抛出错误
2.  返回所有已完成子任务的结果

像往常一样，我将使用一些易于理解的示例代码来帮助您了解这两种方法是如何工作的，所以让我们开始吧！

> **注:**
> 
> 本文要求您对 Swift 任务组有一个基本的了解。如果您不熟悉任务组的基础知识，我强烈建议您首先阅读我以前的文章“[通过示例](https://swiftsenpai.com/swift/understanding-task-groups/)了解 Swift 任务组”。

# 定义抛出子任务

为了演示任务组中的错误处理，我们必须首先有一个可以抛出错误的子任务。让我们修改我们之前创建的[的`SlowDivideOperation`](https://swiftsenpai.com/swift/understanding-task-groups/)，这样当除数为零时它将抛出一个错误:

如你所见，`execute()`函数现在用`throws`关键字标记，表明它现在是一个抛出函数。除此之外，我还添加了 2 个打印语句来帮助我们可视化执行操作时实际发生的情况。

有了这些，我们现在可以开始研究第一种方法了。

# 方法 1:使用抛出任务组抛出错误

出于演示的目的，我们将创建一个任务组，它产生多个子任务，这些子任务执行一个`SlowDivideOperation`并返回它的名称和结果。当所有的`SlowDivideOperation`完成后，任务组将收集它所有的子任务结果，并返回一个包含所有`SlowDivideOperation`名字和结果的字典。

如果任何子任务遇到错误，它将抛出错误并将错误传播到任务组，任务组将抛出错误。下面是示例代码:

让我们看一下上面代码的一些重要细节:

1.  `operations`数组定义了任务组要产生的子任务。请注意，当我们执行示例代码时， *"operation-2"* 会抛出一个错误。
2.  我们将使用`withThrowingTaskGroup(of:returning:body:)`功能创建一个投掷任务组。它的工作方式类似于`withTaskGroup(of:returning:body:)`函数，但是我们需要使用`try`关键字来调用它，因为它可能会抛出一个错误。
3.  我们必须用`try`关键字调用`SlowDivideOperation`的`execute()`函数。这允许由`execute()`函数抛出的错误传播到任务组。
4.  因为我们现在使用一个抛出任务组，所以当从每个子任务收集结果时，我们必须使用`try`关键字。

现在，如果我们尝试执行示例代码，我们将得到以下输出:

```
✅ operation-1 completed: 2.0
⛔️ operation-2 throw error
✅ operation-3 completed: 4.0
✅ operation-0 completed: 5.0
👎🏻 Task group throws error: divideByZero
```

上面的输出显示了我们所期望的结果—*“operation-2”*抛出一个错误，并且该错误正在传播到任务组，从而导致任务组抛出`divideByZero`错误。

尽管我们的示例代码正在做我们想要的事情，但它并没有被优化。从输出中可以看到，*“操作-3”*和*“操作-0”*仍然会继续执行，直到完成，即使*“操作-2”*已经抛出错误。我们能做些什么来避免这种情况吗？

## 理解投掷任务组的行为

为了优化我们的示例代码，我们必须首先了解当抛出任务的子任务抛出错误时，抛出任务组将如何表现。这里有一些你应该知道的重要行为:

1.  任务组只会抛出其子任务抛出的第一个错误。来自其他子任务的所有后续错误都将被忽略。
2.  当子任务抛出错误时，所有剩余的子任务(仍在运行的子任务)将被标记为取消。
3.  **标记为取消的子任务将继续执行，直到我们显式停止它。**
4.  标记为 canceled 的子任务不会触发从任务组收集结果的 for 循环，即使该子任务已完成其执行。

上述列表中的第三种行为是导致*“操作-3”*和*“操作-0”*继续执行的原因，即使*“操作-2”*已经抛出错误。要显式停止一个被取消的任务，我们可以使用`Task.checkCancellation()`方法。这个方法将检查当前正在执行代码的任务，如果任务被取消，它将抛出一个`CancellationError`。

考虑到这一点，让我们将焦点转回`SlowDivideOperation.execute()`方法。对于我们的情况，检查取消的最佳位置是在`Task.sleep()`方法之后。

这就是我们需要做的。现在，如果我们再次执行示例代码，我们将得到以下输出:

```
✅ operation-1 completed: 2.0
⛔️ operation-2 throw error
👎🏻 Task group throws error: divideByZero
```

这样，我们成功地提高了示例代码的效率。一旦一个子任务抛出错误，任务组中所有剩余的子任务都将停止执行。

# 方法 2:返回所有已完成子任务的结果

现在，如果我们想要一个与方法 1 完全相反的结果呢？我们希望我们的任务组忽略所有有错误的子任务，并返回所有已完成子任务的结果。

使用的概念与方法 1 非常相似，但是这次我们将创建一个普通的(非投掷)任务组，并使用`try?`忽略所有错误:

上面的示例代码与方法 1 的示例代码几乎相同，但是有一些重要的区别您应该知道。让我们详细看一下:

1.  我们使用`withTaskGroup(of:returning:body:)`函数创建一个任务组，因为我们的任务组将不再抛出错误。最重要的是，我们必须将子任务结果类型更改为 optional，这样当错误发生时，我们的子任务可以返回`nil`。
2.  调用`execute()`时使用可选的 try ( `try?`，当`execute()`函数抛出错误时返回`nil`。
3.  由于我们的子任务不再抛出错误，我们可以从 for 循环中删除关键字`try`。此外，我们必须将`compactMap`应用到`taskGroup`，以便过滤掉子任务返回的所有`nil`结果。

下面是我们从上面的代码中得到的输出:

```
✅ operation-1 completed: 2.0
⛔️ operation-2 throw error
✅ operation-3 completed: 4.0
✅ operation-0 completed: 5.0
👍🏻 Task group completed with result: ["operation-0": 5.0, "operation-1": 2.0, "operation-3": 4.0]
```

很简单，不是吗？

# 包扎

我在本文中向您展示的两种方法只是在任务组中处理错误的两种最基本的方法。您肯定可以扩展这些方法中使用的概念来处理更复杂的情况，以满足您的需求。

像往常一样，您可以在 [GitHub](https://github.com/LeeKahSeng/SwiftSenpai-Swift-Concurrency) 上获得本文中的示例代码。

我希望这篇文章能给你一个在使用任务组时如何处理错误的好主意。如果您有任何问题或意见，请随时通过 [Twitter](https://twitter.com/Lee_Kah_Seng) 联系我。

感谢阅读。

# 进一步阅读

*   [通过 Swift 中的 Async/await 发出网络请求](https://swiftsenpai.com/swift/async-await-network-requests/)
*   [在 Swift 中使用 Actors 防止数据竞争](https://swiftsenpai.com/swift/actor-prevent-data-race/)
*   [Swift 中的演员重入问题](https://swiftsenpai.com/swift/actor-reentrancy-problem/)
*   [Sendable 如何帮助防止数据竞争](https://swiftsenpai.com/swift/sendable-prevent-data-races/)