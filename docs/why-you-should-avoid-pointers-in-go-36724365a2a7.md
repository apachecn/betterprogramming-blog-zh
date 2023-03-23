# 为什么在围棋中应该避免指针

> 原文：<https://betterprogramming.pub/why-you-should-avoid-pointers-in-go-36724365a2a7>

## 只在你真正需要的时候使用指针

![](img/5722a9b7c226a6d0f936ea31bbcb162c.png)

[Raychan](https://unsplash.com/@wx1993?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/point?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照——指向上

# 什么是指针？

为了涵盖基础知识，让我们从研究什么是指针开始。

我们来看下面的`CoffeeMachine`例子。这个结构记录了咖啡机里有多少咖啡豆。

为了创建一个新的咖啡机，我使用了`NewCoffeeMachine()`功能。这里，我创建了一个新的结构，并通过使用`&`操作符将引用传递给这个结构。

当我将对`CoffeeMachine`结构的引用传递给其他函数时，它们可以编辑底层结构。

例如，我可以创建一个直接编辑`CoffeeMachine`结构的`SetNumberOfCoffeeBeans()`函数，如下所示:

因为`SetNumberOfCoffeeBeans()`接收到对底层`CoffeeMachine`结构的引用(指针),所以原始结构的`numberOfCoffeeBeans`被直接更新。

因此，当我运行这个程序时，它显示机器中确实有 100 颗咖啡豆！

```
go run main.go
The coffee machine has 100 beans
```

# 不用指针解决同样的问题

我们也可以实现没有指针的咖啡机。

主要的区别是`SetNumberOfCoffeeBeans()`现在接收一个`CoffeeMachine`结构的副本。

因此，我们需要从函数中返回更新后的`CoffeeMachine`结构。

运行这个程序时，程序仍然工作，输出也是一样的。

```
go run main.go
The coffee machine has 100 beans
```

# 表演

好吧，你可能在想:“通过值传递和一直复制结构可能不如传递指针有效。”

这里有一篇很好的文章解释了为什么使用指针不一定是性能优化。为了好玩，让我们尝试一个实际的测试，比较使用指针和通过值传递的性能。

我已经更新了`CoffeeMachine`结构来保存`UID`和`Description`。

接下来，我使用指针将这些值设置 100，000 次，并测量需要多长时间。

我也做了同样的事情，但是这次没有指针。

两个程序花费的时间大致相同。

```
With pointers result:     32ms
Without pointers result: 31ms
```

现在，我在这个例子中使用的结构非常小。如果你开始复制巨大的结构，可能会有更大的性能差距。

# 偶然突变

那么，使用指针的坏处是什么呢？

当你传递一个指向函数的指针时，你不知道它是否被编辑过。

这增加了代码库的复杂性，随着代码的增长，很容易出现错误，因为在调用堆栈的深处，指针结构发生了变化。

最近在一个项目中，碰到一个搜索产品的函数:

看到这个功能，没想到`SearchCriteria`也变了。然而，事实证明，在这个函数的深处，criteria 结构实际上发生了变化。

在我看来，尽可能使用不可变的参数——即值而不是指针——是一种更好的实践，可以避免这种代码味道。

# 零值

每当你引入一个指针，你就引入了一个`nil`值的可能性。用户在使用某个指针值之前，不会被明确地强制检查该指针值是否为`nil`，因此人为错误很容易在代码库中蔓延。

考虑下面的例子。

在这个例子中，`GetProduct()`返回一个空值，我们不强制检查这个值。当运行这段代码时，我们得到一个`nil pointer`错误。

```
panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0x1 addr=0x8 pc=0x10994f3]goroutine 1 [running]:
main.main()
 main.go:17 +0x23
exit status 2
```

解决这个问题的更好的方法是，如果没有找到产品，则返回一个空结构和一条错误消息。

这样，用户被迫检查错误，而您绝对确定不会出现`nil pointer`错误。

# 何时使用指针

好吧，指针并不总是邪恶的。在两种情况下你*应该*使用指针。

## **1。当你真的需要直接修改一个参数**

例如，在这段代码中，在指针的帮助下，在`setName`函数中修改了用户名。

## 2.当你需要一个独生子的时候

有时候，某样东西只有一个实例是很重要的。例如，内存中的数据存储不应该总是被复制。

# 结论

不要疯狂地使用指针，而是仔细考虑何时以及如何使用它们。

如果你遵循这个建议，你将(几乎)再也看不到`nil pointer dereference`错误了！