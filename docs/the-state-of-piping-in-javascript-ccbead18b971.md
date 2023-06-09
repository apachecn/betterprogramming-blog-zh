# JavaScript 中管道的状态

> 原文：<https://betterprogramming.pub/the-state-of-piping-in-javascript-ccbead18b971>

## 管道如何提高代码的可读性，以及我们目前对原生管道支持的立场

![](img/3b3dac1f1d1b220fd5b99f19aa24606c.png)

照片由 [Erlend Ekseth](https://unsplash.com/@er1end?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在编程中，函数组合是将简单函数组合在一起以创建更复杂功能的行为。每个函数的结果作为参数传递给下一个函数，最后一个函数的结果成为整个合成的结果。以这种方式组合在一起的功能通常被称为管道，这类似于物理管道，因为信息在管道中单向流动，就像水在物理管道中流动一样。

如今，在 JavaScript 中将函数链接在一起已经成为可能。这里有一个简单的例子:

这并不坏——我们已经将代码分解成三个可重用的组件，然后依次调用它们来创建期望的结果。一个函数的结果作为参数传递给下一个函数，最后一个函数的结果是我们的最终结果——本质上，我们有自己的管道。不过，这个例子在可读性方面有其局限性。这个序列中的功能执行是`repeat`，接着是`capitalize`，最后是`exclaim`——基本上是从右到左。然而，拉丁语言(比如英语)是从左向右读的，这使得我们的管道有些违反直觉。

这给我们带来了一个提议的新操作符，它可能在将来使事情更可读。

# 管道运营商提案

为了更好地以可读和功能的方式支持链式函数调用，有一个开放的 [TC39 提案](https://github.com/tc39/proposal-pipeline-operator)引入了管道操作符。管道操作符`|>`的灵感来自函数式语言中的等价操作符，如 [F#](https://en.wikibooks.org/wiki/F_Sharp_Programming/Higher_Order_Functions#The_.7C.3E_Operator) 和[elixin](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2)以及常规的 Unix 管道。

使用建议的管道操作符，我们可以解决上一个示例的可读性问题:

好多了——现在我们有了可重用的组件，我们可以以一种可读的方式从左到右(或者从上到下，如上面的例子)组合它们。

这是一个非常基本的例子，每个函数使用一个参数。让我们看另一个有多个参数的例子。

这还不算太糟。我们使用管道操作符的例子比最后一行没有管道操作符的等价例子更易读。不过，处理管道中多个参数所必需的箭头函数增加了噪音，使代码更难阅读。

为了解决可读性问题，我们转向另一个 [TC39 提案](https://github.com/tc39/proposal-partial-application)——即部分应用提案。在程序设计中，部分应用是将许多参数固定到一个函数上，产生另一个具有较少参数的函数的行为。根据该提案:

> 这个提议引入了一种新的语法，在参数列表中使用了`?`标记，这允许您通过充当参数的占位符，将参数列表部分地应用于调用表达式。

使用部分应用程序语法，我们可以提高多参数管道的可读性，如下所示:

这是令人满意的——移除箭头函数极大地提高了我们示例的可读性。现在多参数管道读起来几乎和我们的单参数管道一样容易。

这一切看起来很有希望，但你可能会想，这其中有什么猫腻？不幸的是，事实证明有一些。

首先，管道运营商和部分应用的提案仍处于第一阶段，这通过 [TC39 流程](https://tc39.es/process-document/)意味着，“委员会期望投入时间来检查问题空间、解决方案和交叉关注点。”在这一阶段，无论如何也不能保证提案能够达到标准。

此外，对于管道运营商来说，目前有两种竞争方案——[F #管道](https://github.com/valtech-nyc/proposal-fsharp-pipelines/blob/master/README.md)和[智能管道](https://github.com/js-choi/proposal-smart-pipelines/blob/master/readme.md)。本文不会详细介绍这两种方案的不同之处。两个提议都有一些基本的共同点，比如使用`|>`作为管道操作符。它们在如何处理同步一元函数的最基本用例上也有重叠，就像我们的第一个例子，函数采用单个参数。然而，这些提议在许多事情上确实不同，比如如何处理异步函数、部分应用程序等等。

看看这些提案，它们已经存在了将近三年，但还没有进入第二阶段。问题之一是委员会还未能就推进哪项提案达成一致。GitHub 上有一个长期的[讨论，是关于用一个最小的提议向前推进的想法，但不幸的是，这似乎现在也停滞不前了。](https://github.com/tc39/proposal-pipeline-operator/issues/167)

标准委员会通过共识运作，考虑到决策对语言的影响，这是合理的。正如一位 TC39 委员会成员[在 GitHub](https://github.com/tc39/proposal-pipeline-operator/issues/167#issuecomment-622421783) 上所说:

> 我们在 TC39 中的风格倾向于更保守一点，我们试图推迟到一个特性“完成”之后

另一方面，当委员会未能就如何推进达成共识时，提案(如管道运营商)就有被卡住的风险。

就我个人而言，我希望委员会能够同意提出一个最低限度的建议，以便开始行动。最低提案本身就是对语言的一个巨大贡献，在未来提案的基础上有所建树有望有助于推进该特性的发展。

# 今天做什么

如果你正在使用一个面向函数式编程的库，比如 [lodash](https://lodash.com/docs/4.17.15#flow) 、 [ramda](https://ramdajs.com/docs/#pipe) 、 [rxjs](https://rxjs.dev/api/index/function/pipe) 或者类似的库，你已经拥有了管道功能。然而，创建一个定制的管道函数就像一行代码一样简单。

在函数式编程语言中，函数组合可以表示为一个高阶函数，本质上是指一个函数至少接受一个函数作为参数，或者返回一个函数作为结果，或者两者兼有。本文不打算进一步阐述高阶函数，但是已经有很多关于 JavaScript 中高阶函数的文章——这里有一篇[文章](https://eloquentjavascript.net/05_higher_order.html)可以帮助您入门。

记住这一点，我们的管道函数只不过是一个将其他函数列表作为参数的函数，返回另一个函数，即我们的管道。我们返回的函数将初始值作为输入，然后依次运行我们的管道函数。

为了用我们的管道支持多个参数，我们可以依靠通常被称为函数 currying 的东西。如果你想要一篇关于 curried 函数和组成的深入文章， [Eric Elliott](https://medium.com/u/c359511de780?source=post_page-----ccbead18b971--------------------------------) 有一篇[的好文章](https://medium.com/javascript-scene/curry-and-function-composition-2c208d774983)给你。简而言之，curried 函数允许您将参数固定到一个需要多个参数的函数，将它变成一个我们可以在管道中使用的一元函数。

我们可以继续在此基础上构建，但是我们已经用一个非常简单的自定义管道函数走了很长的路。可读性不如管道操作符，但也不差。

如果你是一个 TypeScript 用户，你可能也有兴趣知道有一个开放的 [pull request](https://github.com/microsoft/TypeScript/pull/38305) 关于为管道操作符添加 TypeScript 支持的最小提议。如果您使用 TypesScript 并认为管道操作符会为您的代码增加价值，那么值得关注。

感谢您的阅读！