# 实用苗条:使用指南

> 原文：<https://betterprogramming.pub/practical-svelte-the-use-directive-60635671335f>

## 利用一个鲜为人知但极其强大的苗条特征:动作，编写更好的组件

![](img/b754dc3cb15bb35f943524bff5ac9c86.png)

由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

苗条是一个很好的框架。这感觉就像夏天的一缕新鲜空气。然而，特别是在开始时，很容易陷入“这是我在以前的框架中做事情的方式”的陷阱。

我想我们每个人都会遇到这种情况。绝对发生在我身上。我不希望这发生在你身上。这就是这篇文章的目的。

假设您希望一些输入字段自动选择焦点上的所有文本。尤其是当来自 React 这样的框架时，开始创建一个围绕输入的组件包装器(例如，`<input>`变成了`<Input/>`)并在其中添加您的自定义事件处理程序可能会很有诱惑力。

这是一个很好的反应方式，但不太苗条。你会问为什么？

好吧，当您拥有原生 DOM 元素时，Svelte(至少在其当前的版本 v3 中)确实大放异彩。您可以使用转换指令，有条件地切换 CSS 类，轻松绑定到当前值，等等。

有了一个设计良好的 CSS 样式系统，您可以在保持代码可读性和可维护性的同时，按照您喜欢的方式对组件进行一致的样式化。

但是，唉，有时候你想要更多。有时，您可能想写那个包装器。因为你所有的(React or Vue or*insert your reactive framework here*)本能都是这么告诉你的。

抵制诱惑。有更好的方法。一种独特的方式。介绍:[使用指令](https://svelte.dev/tutorial/actions)(也称为“动作”)。

虽然它们是标准教程的一部分，但我已经很久没有使用它们了。部分问题可能是教程中的例子感觉相当具体，似乎并不适用于通常的问题集。

但是一旦我理解了他们的全部潜力，我就完全爱上了他们。

它是这样工作的:你取任何一个本地组件(比如一个输入、一个按钮或一个 div)并给它分配一个f *函数，当组件被挂载*时，这个函数在组件的 DOM 节点上执行。

让我们看看上面的用例(选择聚焦的文本)。假设我们有一个基本输入，就像这样:

```
<input type="text" class="ui-input" placeholder="Name" title="Name">
```

第一步。我们定义了一个函数，它接受一个 HTML 节点(将应用指令的组件的节点)，并将我们的事件侦听器添加到该节点。

selectTextOnFocus 指令

第二步。我们用“use-directive”把这个函数分配给组件。

```
<input type="text" class="ui-input" placeholder="Name" title="Name **use:selectTextOnFocus**>
```

就是这样。是的，就这么简单。现在，只要输入获得焦点，它就会选择所有包含的文本。

一个重要的部分——在标准教程中也有解释——是确保在组件被销毁(即从 DOM 中卸载)时运行必要的清理代码。

这就是返回值`{destroy:…}`的用途。否则，您可能会给应用程序带来内存泄漏。

您为一个元素分配了多个指令。因此，我们可以创建一个名为`blurOnEscape`的新函数，并对其赋值。

```
<input type="text" class="ui-input" placeholder="Name" title="Name use:selectTextOnFocus **use:blurOnEscape**>
```

这条指令的代码也非常简单:

蓝光景观指令

这是上面在一个[交互式 REPL](https://svelte.dev/repl/b4db6313dfeb4b50871a9b43398a6952?version=3.16.7) 中的例子。

上面的例子确实很基础，但是潜力是巨大的。例如，在 Faden，我们编写了带有使用指令的多级拖放树处理，显著地减少了组件本身。

尽管如此，简单的模块化修改器就像上面的一样，通常感觉是最大的生产力助推器。

我很想知道您的使用案例。此外，如果您想了解更多高级用例，请告诉我，例如，如何将 props 传递给指令，并使其与组件状态保持同步。

既然你一直读到最后，这里还有一个例子。这一次在交互式的苗条 REPL 中: [*监听元素外部的点击，并分派一个定制事件*](https://svelte.dev/repl/0ace7a508bd843b798ae599940a91783?version=3.16.7) 。

在菜单和下拉菜单的浮动元素上使用非常方便。