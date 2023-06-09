# 如何像专业人士一样使用 JavaScript 中的链接

> 原文：<https://betterprogramming.pub/how-to-use-chaining-in-javascript-like-a-pro-ff9c1b89d9a9>

## 了解如何利用 JavaScript 最常见的设计模式之一

![](img/954f30b6e12933fd34dfa74142293ea7.png)

NOAA 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果我告诉你有一种更简单、更干净的方式来写这样的东西，会怎么样呢？

确实有，它使用了强大的 JavaScript 设计模式，叫做方法链接。

# 什么是方法链？

JavaScript 中的方法链接，或简称为链接，可以定义为在不引入不必要变量的情况下从一个对象调用一个或多个顺序方法。链接的唯一目的是使我们的代码可读性更好，并减少代码中的冗余。

如果您熟悉 JavaScript promises 中的`then()`和`catch()`方法，那么您已经对链接的工作原理有了一些基本的了解。

看看下面的例子。

注意`then()`和`catch()`是如何使用`.`(点)语法一个接一个地被调用的。这就是我们所说的连锁。如您所见，我们的方法`my_promise()`是链接发生的对象。我们可以看到，对于一个承诺，在我们的承诺被解析后，第一个方法被直接调用。如果我们的承诺被拒绝，那么它跳过`then()`方法，用`catch()`处理我们的错误。

理解用 promises 链接是如何工作的非常简单明了，但是在 JavaScript 中我们还能在哪里使用链接呢？

# 链接用在哪里？

在 JavaScript 中，我们有很多方法可以利用这种设计模式。其中一些比另一些简单得多。让我向您展示 JavaScript 中使用链接的一些最常见的地方。

## **1。字符串**

您可能会看到使用链接的一个常见地方是字符串。

看上面的例子，我们可以看到我们试图从一个日期对象中获取时区偏移量。看看我们在这里声明了多少变量来返回这样一个简单的字符串。准确的说有 8 个。对于这样简单的事情来说，这似乎是非常多余的。

那么我们怎样才能做得更好呢？

当然是在链接的帮助下！

在这里，我们采用了以前使用过的所有相同的方法，但是我们将它们链接在一起以返回相同的输出。请注意，与我们之前的实现相比，这是多么的清晰和易读。除了增加可读性，我们现在有一个声明的变量，而不是 8 个！见鬼，如果我们愿意，我们甚至可以把所有这些都放在一行中。

这就是为什么方法链在现代实践中如此有用。

## 2.**数组** **和对象**

我们看到链接发生的最常见的地方是在数组或对象中。举例来说，假设我们想知道在学期结束时获得 80 分或更高分数的学生的平均成绩是多少。我们可以简单地写成这样。

同样，我们应该能够分析这个例子，并识别出我们对初始数组数据进行了几次操作，以获得我们想要的结果。如果我们看到类似线条`11 & 12`的模式，这应该是一个即时的指示器，让我们知道如何利用链接。

> 注意:链接只作用于相同的数据数组，所以要确保你的方法是从相同的数据集中调用的，而不是从不同的数据集中调用的。

分析上面的例子，你可以看到我们可以将`filter`和`reduce`方法链接在一起，因为它们作用于同一个数组，所以我们可以得到与之前相同的输出。这里使用链接的例子实际上比我们之前的实现要长，但是我们只声明了一个变量而不是 3 个。

现在您已经知道了 JavaScript 中链接工作的基本概念，让我们看看如何在应用程序中有效地使用它。

# 有效链接

现在，我们可以在任何东西上使用链接，但是有些情况下使用链接是没有意义的，并且实际上会增加你的代码。让我告诉你我到底是什么意思。

如果我们研究上面的代码，我们可以看到我们试图在我们的`universe`数组中找到一个人，他的名字包含字符串`rick`，并且奖金高于 500 英镑。现在这看起来像是我们可以有效地使用链接的情况，但是实际上链接两个`filter`方法增加了代码的冗余，我们应该将其减少到一个。

请注意我们是如何消除第二个`filter`方法并在初始过滤器中添加第二个检查的。我们仍然得到相同的输出，并且从上面的实现中删除了不必要的过滤器。在这个例子中，不需要使用链接。

还记得我在本文开头讲的例子吗？现在我们对 JavaScript 中的链接有了更多的了解，让我们看看如何替换冗余并使其更简洁。

在前面的例子中，我们声明了四个变量，现在我们只声明一个变量就返回相同的结果。另外，请注意我们是如何将方法`Object.entries()`包装在方法`reduce()`周围的。这是因为我们需要引用 reduced 对象的输出，而当我们将所有这些方法链接在一起时，我们将无法做到这一点。

现在我们的代码看起来比以前更加结构化，更加整洁，可读性更好。理解在我们的代码中使用链接的有用性和有效性并不需要很长时间。

# 结论

正确使用的话，链接会非常有用和有效。确保只在必要的时候使用它，否则你只会增加更多的冗余代码。当我第一次开始用 JavaScript 写作时，这是一个很难理解的概念，但是你越了解它是如何工作的，你就会越明白它为什么如此有益。

感谢您的阅读，下一期再见。