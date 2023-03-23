# React useMemo 与 useCallback:异同

> 原文：<https://betterprogramming.pub/react-usememo-vs-usecallback-similarities-and-differences-ea2b1f8bb22b>

## 两种反应钩的比较

![](img/68ee2deda237e7bbbcfabfb3f4cc927b.png)

照片由 [Hichem Meghachou](https://unsplash.com/@hichem350z?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 React 开发领域，我们得到的最棒的东西之一就是钩子。早在 2019 年，React hooks 的引入实现了一种全新的处理逻辑的方式。钩子极大地改进了我们在不同组件之间实现、构造、隔离和共享逻辑的方式。

直到三年后的今天，在写这篇文章的时候，我还在发现和学习一些关于某些钩子或其内部的新东西。最近引起我好奇心的事情之一是我经常使用的两个 React 挂钩:`useMemo`和`useCallback`。

# 他们的目的

这两个挂钩背后的目的是通过[记忆](https://en.wikipedia.org/wiki/Memoization)来提高 React 性能。简而言之，记忆化是一种基于输入存储计算值的缓存技术。如果用相同的输入调用该函数，那么它将跳过计算并返回缓存的值。如果输入发生变化，它将重新计算值并缓存。

`useMemo`和`useCallback`都允许 React 开发人员轻松地记忆某些实体，这可以用来潜在地防止组件重新呈现。顾名思义，`useCallback`钩子是专门用来记忆回调的。`useMemo`钩子可以用于所有其他的静态实体，比如原语和对象。

# 相似之处

实际上，`useMemo`和`useCallback`挂钩不仅有相同的目的，而且在用法上也非常相似。两个挂钩:

*   必须在组件的顶层调用，就像所有的钩子一样。
*   接受一个函数作为第一个参数，接受一组依赖项作为第二个参数。
*   根据所提供的函数，返回一个在渲染中引用稳定的记忆值。
*   当在依赖关系数组中检测到任何更改时，根据函数重新计算并记忆值。

# 有区别吗？

那么有趣的问题就变成了:`useMemo`和`useCallback`有区别吗？如果有，它们到底是什么？

正如本文前面已经提到的，一个小的区别是,`useCallback`只能用于记忆回调，而`useMemo`基本上用于所有其他的 JavaScript 实体。它们的目的和用法基本上是相同的，这使得它们所针对的不同实体似乎是钩子之间唯一的区别。

但是从概念上讲，也可以通过使用`useMemo`的`useCallback`来模仿记忆功能的行为。虽然这看起来很奇怪，但是使用`useMemo`并从工厂函数返回一个函数是可能的。理论上，这与使用`useCallback`是一样的。

如果这成立，这也意味着除了方便之外，`useCallback`和`useMemo`之间没有真正的区别。因此，问题变成了通过`useCallback`钩子，通过执行`useCallback(() => doSomething())`来记忆一个函数，和通过从工厂函数返回一个回调，通过执行`useMemo(() => () => doSomething())`来记忆一个函数，这两者之间是否有区别？

# 深入挖掘

在[官方 React hook docs](https://reactjs.org/docs/hooks-reference.html#usecallback) 中，已经有小纸条通知我们`useCallback(fn, deps)`等同于`useMemo(() => fn, deps`。然而，这并没有给我们提供很多信息或背景。比如，这是否适用于所有类型的函数，尤其是内联函数？有边缘案例吗？为此，让我们深入研究一下这两个钩子的实现。

两个钩子的代码都在`react-reconciler`包中，特别是在[文件](https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiber.new.js)中，该文件包含钩子的新 React Fiber 实现。在那里，你会找到所有钩子的代码，它们什么时候挂载，什么时候需要更新。为了发现我们正在考虑的两个记忆挂钩之间的区别，我们必须看看它们的更新函数:`updateCallback`和`updateMemo`。

先说`useCallback`钩子的更新功能:

这里发生了一些事情，但是最重要的事情是对`useCallback`钩子的后续调用以下面的方式处理:

1.  检索用于调用该挂钩的先前依赖关系(状态),并与当前依赖关系进行比较。
2.  如果它们相同，钩子将返回上一次运行的缓存回调。
3.  如果它们不相同，该函数将缓存当前的回调以及当前在`hook.memoizedState`中的依赖关系。这些可能会在后续调用中重用。

现在我们来看看`useMemo`的更新功能实现:

如果我们比较这两个更新函数，我们会发现它们几乎是相同的。在随后的钩子调用中，两个钩子基本上经历相同的过程。我们能发现的唯一区别是被记忆的值。

在`useCallback`的情况下，被记忆的值直接是第一个函数参数。在`useMemo`的例子中，也使用了第一个函数参数，但方式略有不同。我们还可以看到，与`callback`相比，它有一个不同的名字`nextCreate`。钩子不会立即记忆所提供的函数，而是调用它并记忆结果值。

基于此，我们可以重新回到最初的问题，关于`useCallback(() => doSomething())`和`useMemo(() => () => doSomething())`之间是否有区别。如果我们关注后者，这意味着钩子将调用工厂函数，并在 mount 上缓存`() => doSomething()`函数。只要依赖关系不变，它就会一直使用缓存的版本。

这正是`useCallback`所做的，但是没有调用工厂函数，而是使用定义的函数。因为内存化是在 React 钩子中实现的，它只与之前的调用进行比较，这意味着内联定义的函数也可以很好地工作，并且在两个钩子之间完全相同。

# 结论

本文比较了两种常用的用于记忆的 React 钩子，`useCallback`和`useMemo`。它们都是性能反应开发的基础。乍一看，这两个钩子极其相似，以至于人们可以怀疑是否有什么不同。有实际的区别吗？还是为了方便起见，它们用不同的名称包装了相同的逻辑？

为了回答这个问题，本文深入研究了这两个钩子的实现。在这里，我们发现两个钩子背后的逻辑基本上是相同的，除了如何确定内存化的值。考虑到这种实现方式和 React 钩子中内存化的实现方式，内存化一个(内联)函数通常使用`useCallback`还是从`useMemo`返回是没有区别的。

基于此，可以得出结论，两个挂钩之间没有技术差异，两个独立的挂钩只是为了方便而存在。

```
**Want to Connect?**If you liked this article, consider checking out the other entries in the [Uncommon React](https://www.getrevue.co/profile/chakshunyu) newsletter and my [Twitter](https://twitter.com/keraito) for future updates.
```