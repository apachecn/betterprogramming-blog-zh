# 避免在 Redux 中使用 ImmutableJS 的 3 个原因

> 原文：<https://betterprogramming.pub/3-reasons-to-avoid-using-immutablejs-with-redux-b0109d0123e8>

## 一旦进去了，你就再也拿不出来了

![](img/17177e0dc3beeaf0a8c0c7a6ff4d5b06.png)

没有不可变的数据，Redux 就无法工作，因此有意义的是[不可变的 js 库](https://github.com/immutable-js/immutable-js)——它有助于确保不变性——在 Redux 中使用得如此频繁。但是在一些大规模 React-Redux 应用程序中使用 ImmutableJS 之后，很明显使用这个库是有代价的，而且一旦使用，就很难退出。

避免在 Redux 中使用 ImmutableJS 的三个原因是:

1.  它会渗入到哑元件中，使它们变得不纯并且难以使用
2.  它会扼杀 Redux 性能
3.  它允许应用程序状态变得复杂和深度嵌套

在深入这些之前，先简要介绍一下什么是不变性，为什么它在 Redux 中如此重要，以及不变性 JS 提供了什么。

# 不变性和冗余

Redux 通过检查与组件相关的应用程序状态是否发生变化来决定组件是否应该重新呈现。

当应用程序的状态是一个不可变的对象时——这意味着一旦创建就不能再更改，或者*发生了变化—* Redux 可以非常快速和容易地进行这种检查。

这是因为当对象是不可变的时，更新它们的唯一方法是用期望的改变创建一个新对象。新对象在内存中有新的引用，所以只需要一个简单的比较。对于组件关心的每个状态位(在`mapStateToProps`中定义)，Redux 可以做以下事情，不管这个状态位有多复杂:

```
previousBitOfState !== newBitOfState ? updateComponent() : null
```

# ImmutableJS 提供了什么？

ImmutableJS 提供了一系列不可变的数据结构和更新它们的 APIs 它们看起来与普通的 JavaScript 非常相似。

例如，对象可以被处理为:

```
const myImmutableMap = Immutable.fromJS({ a: 1, b: 2 })myImmutbableMap.set('b', 3)
```

和数组表示为:

```
const myImmutableList = Immutable.fromJS([1, 2, 3])myImmutableList.push(4)
```

这确保了`myImmutableMap`和`myImmutableList`永远不会变异，然后在必要时可以使用`toJS()`函数将它们转换回普通的 JavaScript 对象/数组。

ImmutableJS 还在幕后进行了大量性能优化，以使不可变更新快速高效。例如，不变量 JS `List`的`push`和`pop`方法的时间复杂度为 O(1 ),即使它们产生了一个新的`List`。

要向数组中添加一个元素，您必须创建一个包含所有其他元素的数组副本，这通常是一个 O(n)操作。

简而言之，ImmutableJS 是如何使用 Redux 的:

1.  使用`Immutable.fromJS()`将整个 Redux 存储初始化为 ImmutableJS 数据结构。
2.  使用 ImmutableJS APIs 更新您在 reducers 中的状态— `push()`、`set()`、`setIn([])`、`merge()`等。
3.  确信您的状态在更新过程中没有发生变化。
4.  理想情况下，在将 ImmutableJS 状态作为道具传递给组件之前，将它们转换成普通的 JavaScript。

这听起来很简单，但是随着应用程序在代码行和开发人员数量上的增长，很多事情可能会出错。

# 原因 1:它泄漏到哑组件中，使它们变得不纯并且难以使用

Redux 文档清楚地表明，哑组件——例如，那些由 Redux 连接的容器包装的组件——不应该接收 props 作为 ImmutableJS 对象:

> 你的哑元应该是纯净的；也就是说，给定相同的输入，它们应该产生相同的输出，并且没有外部依赖性。如果将这样的组件传递给一个不可变的。JS 对象作为道具，你使它依赖于不可变的。JS 来提取道具的值并操纵它。

还会出现许多其他更明显的问题——尤其是当哑组件同时接收普通 JavaScript 和 ImmutableJS 属性时。当这种情况发生时，开发人员必须时刻意识到他们正在处理的是哪种类型的道具，当试图析构一个 ImmutableJS 对象时，通常会发现困难的方法:

```
const immutableMap = Immutable.fromJS({ a: 1, b: 2 })
const { a, b } = immutableMapconsole.log(a) // undefined
console.log(b) // undefined
```

由于这种不确定性，一些简单的事情变得复杂了——不是不可能的复杂，而是不必要的复杂:

## 1.定义组件的`propTypes`和`defaultProps`

期待`propTypes.object`还是`propTypes.instanceOf(Immutable.Map)`？如果是后者，不要忘记将 ImmutableJS 库导入到组件文件中。

## 2.组件测试的模拟道具

又是同一个问题:期待一个模拟道具是`Immutable.fromJS({...})`还是仅仅是一个普通的`{...}`？

## 3.编写、使用和重用实用函数

一个函数需要一个数组或者不变量列表作为参数吗？它返回什么？为什么有两个函数看起来如此相似？

忽略了这些可以写得更简洁的事实…

为了避免这种不确定性，同时遵守上面引用的 Redux 文档，在传递给组件之前，所有的 props 都必须转换成普通的 JavaScript。这很容易实现——但不是很优雅。

一个天真的方法是在所有道具通过之前使用`toJS()`,但这是一个坏主意，这将在下面的原因 2 中讨论。高阶组件(HoC)可以用来更有效地进行转换，但这需要包装每个已经包装的连接组件，从而产生`connect(toJS(MyComponent))`，其中`toJS()`是一个相当令人生畏的 HoC，看起来像这样:

有一个带有-immutable-props-to-js 的 npm 包[提供了这种特设功能，但是隐藏如此复杂的代码并不会使它更易于使用。](https://www.npmjs.com/package/with-immutable-props-to-js)

# 理由二:它能扼杀 Redux 性能

ImmutableJS 本身针对性能进行了巧妙的优化，但是如果不正确地与 Redux 一起使用——特别是在`mapStateToProps`中——它可能会恶化整个应用程序的性能。我以前曾[写过这个问题](https://medium.com/front-end-weekly/a-redux-performance-obsession-mapping-state-to-props-11b6bbae1766)但没有明确指出不变量，尽管它可能是所有问题中最糟糕的一个。

当使用`toJS()`或`toArray()`将 ImmutableJS 对象转换成普通 JavaScript 时，问题就出现了。

这些操作不仅在大型对象上运行[开销很大](https://www.grzegorowski.com/immutable-js-tojs-vs-toarray)，而且每次都会创建一个新对象。回想一下浅层相等检查，这意味着当 Redux 比较连续的`mapStateToProps`函数的结果时，它们看起来总是不同的——这导致 Redux 连接组件的重新呈现。

<numberscomponent>将比要求更频繁地重新呈现—每次调用 mapStateToProps 时</numberscomponent>

不幸的是，避免在`mapStateToProps`中使用`toJS()`的选项可能很困难，如原因 1 中所讨论的:要么将所有道具保留为 ImmutableJS 对象，要么屈从于另一个 npm 包和/或另一个更高阶的组件。

# 原因 3:它允许应用程序状态变得复杂和深度嵌套

很少考虑应用程序状态的形状，对于小型应用程序来说，这可能很好。但是随着应用程序的增长，状态会变得难以管理——通常是当它变得嵌套很深的时候。

嵌套状态更有可能存储重复数据，当数据可以更新时，这尤其成问题，因为更新必须在所有重复数据之间保持一致。这是为什么经常推荐使用[标准化状态的主要原因之一](https://redux.js.org/recipes/structuring-reducers/normalizing-state-shape/)。此外，在确保不变性的同时更新深度嵌套的数据通常非常困难:

```
const newImmutableState = {
  ...immutableState,
  deeply: {
    ...deeply,
    nested: {
      ...nested,
      data: 'new data'
    }
  }
}
```

这是 ImmutableJS 出手相救的地方之一:

```
immutableState.setIn(['deeply', 'nested', 'data'], 'new data')
```

但是从愤世嫉俗的角度来看，这种语法可能有点太方便了。当更新状态如此容易时，无论有多复杂或嵌套，开发人员都不会质疑它。这使得没有动力去优化状态形状，因此这可能带来的潜在好处(对于开发人员和应用程序性能)将永远不会实现。

# 摘要

ImmutableJS 有助于确保应用程序的状态保持不变，这是 Redux 正常工作所必需的。

然而，使用这个库是有代价的:它会很快遍布你的代码库。甚至进入不关心不可变数据的地方。如果使用不当，它会影响性能。它可能隐藏了有效管理国家的一些细微差别。而且往往看起来甚至不像 JavaScript。

如果你开始一个新的 Redux 应用程序，在单向跳转到 ImmutableJS 之前考虑这些成本，并且知道有替代的解决方案——我已经[在后续文章](https://medium.com/better-programming/try-these-instead-of-using-immutable-js-with-redux-f5bc3bd30190)中写了这些。