# 不要撒谎来对缺失的依赖做出反应

> 原文：<https://betterprogramming.pub/stop-lying-to-react-about-missing-dependencies-10612e9aeeda>

## 了解 useEffect 如何避免 lint 警告和无限循环

![](img/ac745c884526550ca8913c59f11ce44d.png)

由[马克·弗莱彻-布朗](https://unsplash.com/@markfb?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

如果你曾经和`useEffect`一起工作过，你可能会遇到下面的 lint 警告:

```
React Hook useEffect has a missing dependency. Either include it or remove the dependency array.
```

有时这很简单，只需添加所需的依赖项，但这通常会导致一些意想不到的行为，让您摸不着头脑。更糟糕的是，你可能会陷入一个无限循环，因为你在关闭 API 之前拼命地试图关闭浏览器标签…

那么，我们如何才能恰当地处理这一警告，而不是忽视它呢？

*TL；DR:如果你只是在寻找解决方案，你可以直接跳到标题为“用依赖数组解决问题”的部分*

随着 React 钩子的出现，`useEffect`已经被视为一种处理生命周期方法的方式，这些方法以前可以在组件类上访问(例如，`componentDidMount`或`componentWillUpdate`)。现实稍微微妙一些，需要从面向对象编程向函数式编程转变思维。

[根据维基百科](https://en.wikipedia.org/wiki/Pure_function):

> “纯函数是具有以下性质的函数:
> 
> 1.对于相同的参数，它的返回值是相同的(本地静态变量、非本地变量、可变引用参数或来自 I/O 设备的输入流没有变化)。
> 
> 2.它的求值没有副作用(没有局部静态变量、非局部变量、可变引用参数或 I/O 流的突变)。"

在 React 的上下文中，一个纯粹的功能组件是一个接受一些道具(参数)并呈现一些内容的组件。可以肯定的是，使用相同的道具来呈现组件将总是输出相同的 HTML。该组件没有任何内部状态，也不会与任何外部变量交互或修改任何外部变量。

这对于表示组件来说很好，但是在现实世界中，有些情况下我们确实需要副作用(例如，从 API 加载一些数据或者在呈现之间保持一些状态)。这就是 React 钩子的用武之地。

那么`useEffect`到底是做什么的呢？嗯，顾名思义，这是一种处理副作用的方式。它接受一个依赖关系数组，如果自上次渲染以来任何依赖关系发生了变化，它将运行您提供的函数。这意味着我们可以只在需要的时候处理从 API 加载数据之类的事情——而不是每次组件重新呈现的时候。

*注意:如果你对它如何工作感兴趣，React 依赖于组件中钩子的顺序来知道如何处理渲染之间的副作用，这就是为什么你只能在顶层调用钩子。你可以在文档* *中读到更多关于这个* [*的内容。*](https://reactjs.org/docs/hooks-rules.html#explanation)

然而，这种依赖数组经常会导致问题。如果我们想在挂载时运行一次效果，可以使用一个空的依赖数组。例如，假设我们想在组件呈现时使用`[axios](https://github.com/axios/axios)`获取一些数据:

这是您遇到 lint 警告的地方:

```
React Hook useEffect has a missing dependency: 'params'. Either include it or remove the dependency array.
```

这个警告是什么意思？嗯，`useEffect`中的代码依赖于`params`变量来发出 API 请求。当组件重新呈现时，`useEffect`中的`params`的值可能会过期。因为这个效果只运行过一次，所以它在第一次渲染时只有`params`的值。

您可能想知道这是否真的是一个问题，除了会在您的控制台上显示恼人的 lint 警告。在这种情况下，您的代码将按预期工作，没有任何问题。然而，不包括正确的依赖项有可能引入微妙的错误，这可能是一场噩梦。

在他的文章[使用效果](https://overreacted.io/a-complete-guide-to-useeffect/#dont-lie-to-react-about-dependencies)的完整指南(非常值得一读)中，丹·阿布拉莫夫使用了一个带有间隔的计数器应用程序的例子:

您只想设置运行一次的时间间隔，但是由于您的效果没有将`count`作为其依赖数组的一部分，它将只有默认值(在本例中为`0`)。您将看到的是一个`0`,它在一秒钟后增加到`1`,然后不再改变，因为我们在间隔的每个实例上都将计数设置为`0 + 1`。

正如阿布拉莫夫所说:

> “不要撒谎来对依赖做出反应。”

因此，`useEffect`的第一个常见问题是，您只想在挂载时运行一次，但是遇到了 lint 警告。不过，还有另一个问题，我们可以用我们的例子来说明。

您可以尝试通过将`params`添加到`useEffect`的依赖数组来解决 lint 警告:

理论上，这应该行得通。`params`不会改变，所以它应该只在坐骑上运行`useEffect`一次。然而，如果`params`是一个对象，你会发现`useEffect`会在每次组件重新渲染时运行——即使`params`是相同的。

这是因为 JavaScript 不处理对象相等性:

```
{ some: 'value' } === { some: 'value' } = false
```

当`useEffect`检查是否有任何依赖关系发生变化时，`params`将永远不会等于它以前的值，因此效果内部的代码将会运行。

这可能会导致我们的代码陷入一些非常糟糕的无限循环中。那么如何才能解决这两个问题呢？

# 解决依赖数组的问题

和往常一样，当你遇到这样的问题时，第一件要做的事就是后退一步，看看你是否把自己逼入了困境。如果你花点时间重构你所做的事情，通常会有简单的解决方案。例如，您真的应该在组件中进行 API 调用，还是应该将代码解耦并将数据作为道具传递下去？

花一些时间离开，获得一些观点，这意味着你可以经常完全避免这样的问题。

如果你已经做到了这一点，除了使用`useEffect`别无选择，那么是时候使用`useRef`了。

## 拯救的参考文献

React 的`useRef`钩子给了我们一种在渲染之间存储可变值的方法。从[反应文件](https://reactjs.org/docs/hooks-reference.html#useref)中:

> "`useRef`返回一个可变 ref 对象，其`.current`属性被初始化为传递的参数(`initialValue`)。返回的对象将在组件的整个生存期内保持不变。

我们可以使用一个引用来跟踪一些代码是否已经运行。如果没有，那么运行它。如果有，那就跳过它。在我们的示例中，这看起来像这样:

在`useEffect`中，我们检查`hasFetchedData.current`是否是`false`。如果是，那么我们点击 API 并将 ref 更新为`true`。这意味着下一次组件渲染时，`hasFetchedData.current`将会是`true`，我们将不会进行 API 调用。`useEffect`仍然会在每次渲染时运行，但是 API 调用只会运行一次。

实际上，我们有一个“门”我们应该只在满足某些条件时运行`useEffect`中的代码。

## 处理非原语

如上所述，依赖数组中的非原语(即对象或数组)将导致`useEffect`在每次渲染时运行，因为 JavaScript 无法确定对象是否相等。为了解决这个问题，当`useEffect`运行时，我们需要在新旧值之间进行某种形式的深度比较。

这意味着我们需要保存一个对依赖关系先前值的引用，这样我们就可以在`useEffect`运行时将其与新值进行比较，并检查它是否发生了变化。再次，裁判来拯救。我们可以使用`useRef`来存储之前的值，如果值已经改变，就在`useEffect`中更新它(我们可以使用某种深度比较函数来检查)。

假设我们想做类似于上面例子的事情，但是我们想在每次`params`值改变时获取数据。仅仅在依赖数组中传递`params`将意味着`useEffect`在每次重新渲染时运行，这不是我们想要的。相反，我们可以这样做:

*注意:在这个例子中，我们使用的是* `[*fast-deep-equal*](https://www.npmjs.com/package/fast-deep-equal)` *，但是任何深度比较函数都可以。*

让我们来分解一下:

1.  首先，我们设置一个 ref 来跟踪先前的`params`值。
2.  在`useEffect`中，我们对`prevParams.current`和`params`进行了深入的比较，只有当它们不相等时才进行 API 调用。
3.  最后，我们将我们的`prevParams.current`引用更新为新的`params`值。

这意味着 API 调用将只在`params`改变时运行。

# 结论

与`useEffect`一起工作是突显出钩子对于 React 来说有多大的范式转变的事情之一。它真的迫使你去思考到底发生了什么，当它减慢了实现一个简单功能的过程时，这通常会令人沮丧。再次引用[阿布拉莫夫的文章](https://overreacted.io/a-complete-guide-to-useeffect/)谈到用钩子开始:

> “不会有太多要学的。事实上，我们会花大部分时间去忘却。”

然而，一旦你真正开始“用钩子思考”，你就会收获好处。花时间真正理解发生了什么意味着你最终会写出更干净、更有性能的代码。

这些 lint 规则的存在是有原因的，所以我们都应该尽最大努力遵守它们——不要再对依赖关系撒谎了。