# 选择已损坏的角度

> 原文：<https://betterprogramming.pub/selectuntildestroyed-custom-rxjs-operator-to-use-with-ngrx-428baf7a482d>

## 用于 NgRx 的自定义 RxJS 运算符

![](img/1cca273651c8858efbded70691cc2e45.png)

当使用 [NgRx](https://ngrx.io/) 时，我们很大程度上依赖于选择商店的部分来构建我们的组件。避免组件内订阅是一件好事，所以我们通常使用容器来保存选择器返回的可观察数据，并使用异步管道来传递实际数据，以供呈现者显示。

有时候，我们确实需要订阅选择器，每次订阅之后都需要退订。开箱即用，取消订阅大量可观察事物的最佳方式如下:

这意味着，对于每家`select`商店，我们需要:

把`select`和`takeUntil`组合在一起会很棒，对吧？我们可以通过编写一个自定义的 RxJS 操作符来实现这一点，如下所示:

缺点是为了使用它，我们仍然需要定义被破坏的组件(`Subject`，调用`next`，并对其调用`complete`:

如果我们能找到一种方法来包装这个`Subject`的创建，即`takeUntil`，并监听组件的`ngOnDestroy`来触发`next`和`complete`，那就太棒了。这就是 [ngneat/until-destroy](https://github.com/ngneat/until-destroy) 发挥作用的地方。他们提供了一个定制的操作符，正是我们所需要的。

通过将`untilDestroy`组合到我们的自定义操作符中，并添加预期的类型声明，我们可以得到:

我们使用它的方式是:

*注意:如果你正在使用视图引擎，使用*[*ngx-until-destroyed*](https://github.com/orchestratora/ngx-until-destroyed)*。不同的是如果你没有* `*@UntilDestroy()*` *decorator 而你需要写你的* `*ngOnDestroy*` *，它可以是空的。*

# 结论

就是这样！通过创建一个定制的 RxJS 操作符来减少样板文件的快速简单的方法。

希望有帮助。