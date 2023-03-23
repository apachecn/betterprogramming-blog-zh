# 如何在 React 中写出更好的功能组件

> 原文：<https://betterprogramming.pub/how-to-write-better-functional-components-in-react-bc974f777145>

## 可读性和优化的 5 个简单技巧

![](img/0fdde0c5543c9946ee7ddef5cbbc1389.png)

阿诺德·弗朗西斯卡在 [Unsplash](https://unsplash.com/photos/f77Bh3inUpE) 上拍摄的图片。

我们这些在 React 中使用过功能组件的人都知道，钩子的引入让我们的生活变得简单多了。然而，功能组件有它们自己的复杂性和缺陷。因此，有时很难编写可读和优化的功能组件。今天，我们将看看五个简单的提示来帮助我们做到这一点。

# 1.记忆复杂数据

让我们来看看下面这个名为`SortedListView`的 React 组件:

该组件接受一个项目数组，根据提供的比较函数对它们进行排序，并显示它们。然而，如果项目数量大或比较功能复杂，该排序操作*可能会*花费大量时间。这可能最终成为一个瓶颈，因为组件将在每次重新渲染时重新排序项目，即使`items array`或`comparisonFunc`没有改变，但其他一些属性或状态改变了。

这只是一个示例用例。在前端对大而复杂的数据进行排序应该很少见。但是，我们可以通过记忆排序操作并仅在`items array`改变时重新排序来提高 CPU 效率。

> **更新:**然而，对于我们自己控制的组件，我们总是可以防止不必要的重新渲染。我们可以通过避免将键值对象传递给`useEffect`和其他类似钩子的依赖数组，而是传递原始值来做到这一点。
> 
> 要查看`useMemo`的实际性能增益和性能权衡基准，请查看以下链接。

[](https://medium.com/swlh/should-you-use-usememo-in-react-a-benchmarked-analysis-159faf6609b7) [## 你应该在 React 中使用 useMemo 吗？基准分析。

### 我们的一些开发人员最近提出了一个问题，什么时候我应该在 React 中使用 useMemo？在本文中，我们…

medium.com](https://medium.com/swlh/should-you-use-usememo-in-react-a-benchmarked-analysis-159faf6609b7) 

使用`useMemo`挂钩可以轻松实现记忆:

因此，我们可以使用`useMemo`通过交换一些内存来将结果存储或缓存到昂贵的操作中。也就是说，在操作开销不大或者对象没有被传递到任何其他组件的情况下，不需要使用`useMemo`，如上面的链接所示。

# 2.记忆回调函数

就像数据一样，我们也可以记住一个组件传递给它所呈现的其他组件的回调函数。这样做的一个好处是，在某些情况下，它将防止无用的重新渲染。为了说明这一点，让我们看看名为`SortController`的组件如何使用上面的`SortedListView`组件:

在上面的例子中，如果你转到结果选项卡并在文本字段中键入一个新的标题，这将导致`SortController`重新渲染。因此，`ascendingFn`和`descendingFn`将被重新创建。这导致`comparisonFunc`改变。由于`SortedListView`中的`useMemo`依赖于`comparisonFunc`，所以即使`comparisonFunc`没有逻辑变化，它也会重新排序项目。

我们可以通过将`ascendingFn`和`descendingFn`包裹在`useCallback`中来解决这个问题。这个钩子是用来记忆函数的。注意，我们在这里没有为`useCallback`传递依赖数组中的任何东西，因为它们不依赖于组件内部的任何东西。

就像`useMemo`一样，如果函数没有传递给任何组件，并且不是任何其他钩子的依赖项，就不需要使用`useCallback`。

# 3.分离不依赖于组件的功能

我们可以对上面的函数进行的另一个改进是将`ascendingFn`和`descendingFn`移到`SortController`之外。这是因为这些函数不依赖于组件内部的任何东西。所以，真的没有必要在组件内部定义它们。如果我们这样做，组件的可读性会更好。此外，我们不再需要使用`useCallback`，因为这些函数不会在每次重新渲染时重新创建。

我们也可以将`sort`实用函数保存在另一个文件中并导入它们。

# 4.创建子组件

创建子组件是编写优化的、可读的 React 代码的有用方法——即使是使用类组件。子组件将代码库分成更小的、可理解的和可重用的块。这也使 React 更容易优化重新渲染。因此，将大组件分成小组件通常是个好主意。

# 5.创建和重用自定义挂钩

就像组件一样，我们可以创建定制的可重用钩子。这使得代码可读性更好，因为代码库被分成了更小的、可重用的块。在我们的例子中，我们可以将排序逻辑放在一个名为`useSorted`的定制钩子中。

# 结论

为了在 React 中编写可读性更好、更优化的功能组件，我们可以采用这五个简单的技巧。请随意分享您自己编写更好的功能组件的技巧。