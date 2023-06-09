# 看看 Javascript 闭包

> 原文：<https://betterprogramming.pub/a-look-at-javascript-closures-6de9b9ac4f8b>

## 理解闭包在其生命周期中是如何工作的

![](img/73d3c746a32f443252de6c939bc7e100.png)

关闭

在我们之前的博客文章中，我们已经看到使用块范围的变量而不是函数范围的变量是维护代码的更准确的方法。

我们可能会无意中改变用函数作用域定义的变量的值，而没有意识到这一点。所以我们不应该在外部函数中改变内部函数中定义的变量。

这就是终结发挥作用的地方。闭包用于防止变量被外部函数访问。

为了更好地理解闭包在 Javascript 中是如何工作的，我推荐查看博客[***【Javascript 作用域一览】***](/a-look-at-javascript-scopes-acb0f5e9cd77) 和[***【Javascript 作用域和闭包——词法作用域一览】***](/scope-closure-lexical-scope-9a18d0579d66)

# 内容

*   什么是闭包？
*   闭包生命周期
*   为什么关闭

# 什么是闭包？

闭包是函数的行为。无论是在对象上还是在类上，都没有任何形式的闭包。简而言之，只有函数有闭包。

让我们继续上面的代码示例，以便更好地理解闭包。简单看一下上面的例子。

每次调用`employeeCreator`函数时，不应该期望`id`变量为 0 吗？但是每次调用`employeeCreator`函数时，它返回的值都比前一个值多 1。那为什么呢？答案:闭包。

每次调用函数时，垃圾收集器都必须移除该变量。在这里，垃圾收集器不会删除该值，引用仍然存在。因此，变量 I 是从引用中读取的，而不是在每次调用函数时被重新定义。

如果引用被垃圾收集器删除了会怎么样？如果没有闭包并且引用被垃圾收集器删除，那么由`employee`、`employee2`、`employee3`变量调用的`employeeCreator`函数的引用将被删除，因此这三个变量的值将是未定义的。

# 闭包生命周期

当且仅当对函数的引用被删除时，绑定到该函数的作用域才会被破坏。

没有被 GC 清除的闭包会累积并导致内存使用失控。

不使用的闭包必须从应用程序中清除。

闭包的清理是影响应用程序性能的一个重要标准。

`changeInputEvents`函数是以输入元素为参数，返回函数的函数。它在返回的函数中接受一个参数，这个参数就是一个函数。

如果在调用此函数时将方法作为参数给出，它会将此方法添加到已定义输入的 change 事件中。

如果在没有给定任何参数的情况下触发该函数，它将清除输入的 change 事件中的所有方法。

如果在上面代码片段的第 34 行中没有用空参数触发函数，那么我们程序中的垃圾收集器就不会清除这两个函数的引用，所以它会占用不必要的内存。

# 为什么要关闭？

到目前为止，我们已经了解了闭包是什么以及它们是如何工作的。

那么我们为什么需要闭包呢？闭包给了我们什么？

让我们快速看一个例子。

这里，第 1 行到第 9 行和第 10 行到第 20 行这两个方法执行相同的操作。

首先，我们从第一部分开始。在`DisplayText`函数中选择一个`id`为`displayArea`的元素，然后打印出`innerText`值在函数参数中的文本值。

如该函数所示，执行了两个操作。让我们在 DOM 中找到一个元素，然后改变元素的`innerText`。每次触发该功能时，都会发生这两个步骤。

那么我们真的需要每次都选择相同的元素吗？反正每次不都是选同一个元素吗？而且，如果我阻止删除这个值的引用(如果我不需要再次选择它)，我不会在性能方面加强程序吗？

第二部分，有一个函数叫做`manageDisplayText`，它首先在 DOM 中选择一个元素，然后返回一个函数。这里找到的函数接受文本参数，并将参数中的值打印到 DOM 中所选元素的`innerText`中。

虽然其功能相同，但第二部分中的功能更有效。因为第一次触发`showText`功能的时候，heading 变量是被创建的，并没有被删除。第二次触发`showText`时，航向值已经存在，只有方法的`innerText`变化起作用。

感谢阅读。