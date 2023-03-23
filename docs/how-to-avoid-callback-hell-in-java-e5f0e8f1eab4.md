# 如何避免 Java 中的回调地狱

> 原文：<https://betterprogramming.pub/how-to-avoid-callback-hell-in-java-e5f0e8f1eab4>

## 不要把你的 JavaScript 代码味道带到 Java 中

![](img/a5988c5e54b30a16e1468861b0971460.png)

在 [Unsplash](https://unsplash.com/s/photos/pyramid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[Nour wagh](https://unsplash.com/@nourwageh?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片。

JavaScript 编码人员认识到深度嵌套的回调是处理异步代码的原始方式，并为其命名。因为 JavaScript 只有一个线程，所以阻塞是不允许的，所以任何有 I/O 的代码都需要有一个函数，当请求完成时，这个函数就会被调用。所以你会有这样的代码:

当阻塞方法都将函数作为参数时，这是编写代码最直接的方式，但是阅读它会让您头疼。如果代码变得太宽，它就会滚出屏幕。当然，你可以以此为借口购买更宽的显示器，或者你可以习惯水平滚动。但是我更喜欢适合屏幕的短线条。

在现代 JavaScript 中，通过使用承诺可以很容易地避免这种情况。当异步方法返回承诺时，您可以链接方法:

这使代码看起来更有命令性(“做这个，做那个，做另一个”)，因为您阅读代码时仍然处理 JavaScript 的异步特性。

但是承诺是一个有些开发人员难以理解的概念。虽然它看起来更干净，但它没有异步调用那样明显的本质，所以一些 JavaScript 开发人员仍然回退到回调。多年来一直与回调打交道的开发人员已经习惯了回调，并不认为可读性是个问题。但是，如果我正在进行代码审查，并且我看到了深度嵌套的结构，这是一个危险信号。

这和 Java 有什么关系？

现在，以反应式 Java 形式出现的异步 Java 变得更加流行，我们有同样的问题要处理。尽管 reactive Java 有一个类似于 promises 的 API，但可能有一些开发人员来自 JavaScript 回调背景。或者可能有同样的感觉，写代码时回调更容易处理。

在 reactive Java 中，处理调用其他回调的回调的正常方式是通过`flatMap`方法。名称`flatMap`来自于`Streams`库，在那里你可能有一个流的流，你想把它们“展平”成一个包含所有项目的单个连续流。它在 reactive Java 中做同样的事情，但更重要的是，`flatMap`“订阅”内部序列。`subscribe`函数是指示你的回调函数的一种方式。不幸的是，名称`flatMap`并没有真正描述它在无功系统中的价值，因此可能很难理解。

考虑以下代码:

是的，可以用，但是很难看。但是如果你和来自回调风格的 JavaScript 背景的人或者不理解`flatMap`为你订阅的人一起工作，你可能会看到这一点。

让我们用 flatMap 重写它，这样我们可以看到它是如何避免(或者更准确地说，隐藏)所有的`subscribes`:

正如 promises 通过以线性方式呈现步骤使代码更具可读性一样，正确地使用 reactive Java 可以使异步方法更易于阅读和编写。也可以降低每一步的密度。`database.lookup(database.queryBuilder(wdata))`线有点密。让我们把它展开一点:

现在代码稍微长了一点，但是没有那么宽。对我来说，每一行执行一步很好，这样我就可以一步一步地阅读正在发生的事情。因为它的长度比宽度大，所以作为一个要做的事情的清单更有意义。此外，如果您正在进行代码审查，并且不得不并排查看旧代码和新代码，那么就更容易看到哪些步骤被更改、删除或插入了。

学习`flatMap`的魔力，你将在掌握反应式 Java 的道路上走得很远。我希望这个小演示能让你更容易理解。本文的 Java 代码可以在下面找到:

[](https://github.com/rkamradt/flatmapvssubscribe) [## rkamradt/flatmapvssubscribe

### 与我的文章配套的代码避免 Java 中的回调地狱

github.com](https://github.com/rkamradt/flatmapvssubscribe)