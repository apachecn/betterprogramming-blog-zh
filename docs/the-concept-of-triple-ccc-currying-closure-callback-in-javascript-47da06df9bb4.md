# 三重 CCC 的概念:JavaScript 中的 Currying、闭包、回调

> 原文：<https://betterprogramming.pub/the-concept-of-triple-ccc-currying-closure-callback-in-javascript-47da06df9bb4>

## 了解这些概念可以帮助你在面试中脱颖而出

![](img/54fd85746d0ac670ec4cfee74815330b.png)

由[约书亚·阿拉贡](https://unsplash.com/@goshua13?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

JavaScript 是一种编程语言，用于前端和后端来构建交互式 web 应用程序。它是浏览器唯一能理解的脚本语言。迄今为止，几乎 98%的网站在客户端使用这种语言来处理网页行为。它是世界上最流行的编程语言。

但是这门语言的旅程并不顺利。这种语言由`Brendan Eich`设计，于 1995 年 12 月 4 日首次出现。最初，它只是用来给网页带来交互性，而 HTML 和 CSS 是用来构建和设计网页的。

当`Ryan Dahl`在 2010 年首次引入 NodeJS 时，改变游戏规则的时刻发生了。它只不过是一个开源的、跨平台的后端 JavaScript 运行时环境，运行在 V8 引擎上，在 web 浏览器之外执行 JavaScript 代码。

从那时起，这种语言被广泛使用。在 2022 年，学习这种令人惊叹的语言是值得的，以便在 Web 开发领域建立强大的职业生涯。为了让你的学习之旅更加顺利，也为了夯实你的基础，今天，我将讨论 JavaScript 的概念，它们是 Currying、Closure 和 Callback (CCC)。

不管你现在在哪个位置！如果你想成为一名优秀的 JavaScript 开发人员，那么你需要对这些主题有一个坚实的理解。此外，这将有助于你破解面试问题，因为大多数时候面试官会问一些关于这些话题的常见问题。在本教程结束时，您将对 JavaScript 中的 Currying、Closure 和 Callback 有一个清晰的理解。

# 您将从本教程中学到的东西

我们将在本教程中讨论以下主题:

*   充当一等公民
*   Currying
*   关闭
*   回收

# 充当一等公民

在了解 CCC 的概念之前，你需要理解一等公民的概念。在 JavaScript 中，函数被称为一等公民有以下三个原因

*   一个函数可以被赋值给一个变量
*   一个函数可以作为参数传递给其他函数
*   一个函数可以由另一个函数返回

让我们来看一些代码片段，它们展示了 JavaScript 中的函数如何满足上述三个原因

# 一个函数可以被赋值给一个变量

在这里，你可以看到我们已经声明了一个箭头函数，稍后，我们将它存储到一个名为`myFunc`的变量中。

# 一个函数可以作为参数传递给其他函数

我们现在接受一个函数参数，而不是在函数`myFunc`中硬编码“Hello”。正如你所看到的，有三个不同的函数`hello`、`bonjour`和`salut`，它们中的每一个都作为参数传递给`myFunc`

# 一个函数可以由另一个函数返回

我们有一个名为`outerFunc`的函数。它没有做太多事情，只是简单地向控制台打印一条语句。内部创建了另一个名为`innerFunc`的函数。`innerFunc`也做不了什么，它只是向控制台打印一条语句。`outerFunc`返回`innerFunc`。

有几种方法可以使用它。第一种方法是一起使用两个大括号。第一对括号调用`outerFunc`，第二对括号调用`outerFunc`返回的对象，即函数`innerFunc`。

在第二种方法中，我们将返回的函数存储在一个变量中，并像普通函数一样调用它

# Currying

简单地说，Currying 是一种链接多个函数的技术。这是一个函数式编程特性。让我们通过解决一个常见的面试问题来理解这个概念:

**问题**

请看下面的代码片段。

你需要执行的任务是调用 sum 函数比如 sum(10)(20)(30)而不是 sum(10，20，30) this。

```
// Current way of Invoking function
sum(10, 20, 30)// Desired way of Invoking function
sum(10)(20)(30)
```

你如何执行这个动作？答案很简单，你需要使用的术语是奉承。请参见下面的代码示例

在这里，你可以看到我们在另一个函数中声明了一个函数，并且创建了一个函数链。你也可以看到我们得到了完全相同的输出，这个过程在 JavaScript 中被称为 Currying。

Currying 在流行的 javascript 库中使用，例如 [Lodash](https://lodash.com/) 和 [Rambda](https://ramdajs.com/)

# 关闭

JavaScript 中的闭包只不过是一个从内部作用域引用外部作用域中的变量的函数。让我们在下一节看看 closure 解决的问题，这样你就能知道为什么我们需要

在这里，您可以看到我们得到了预期的输出。但主要问题是我们如何得到它？让我们在下面的部分进行分解:

首先，我们声明了一个名为`sayHello()`的函数，并在`name`变量中存储了一个字符串。后来，我们声明了另一个函数，并用`Hello`消息打印其名称，并返回了`inner()`函数。最后，我们将返回的函数存储到了`abc`变量中。

现在，当 JavaScript 执行代码时，它将找不到`name`变量，因为`sayHello()`函数的内存已经被破坏了。但是我们如何获得预期的输出呢？

这背后的主要原因是关闭。这里，`sayHello()`函数的内存确实被破坏了，但它创建了该函数的引用，因此，在执行`inner()`时，它可以从`sayHello()`函数的引用中访问变量。要执行这个操作，您不需要编写一行额外的代码。因为 JavaScript 引擎足够智能来执行这个动作。你需要做的就是理解闭包的概念，默认情况下，每次在 JavaScript 中创建一个函数时，它都会被创建。

# 回收

回调是作为参数传递给另一个函数的函数。下面我们来看一个用例。

假设，我们在数据库中保存了一个姓名数组。我们希望添加一个新的名称，稍后，我们希望打印带有新添加名称的名称。为了执行此操作，我们编写了以下代码:

在这里，您可以看到，我们添加了添加新名称的功能，但在输出中，新名称尚未添加。这背后的主要原因是 JavaScript 的`asynchronous`行为。我们知道，对于需要很多时间的任务，JavaScript 不会等待它们，而是处理其他较轻的任务，并将较重的任务放在队列中。同样的事情也发生在这里。

我们需要使用一个回调函数，这样我们就可以打破 JavaScript 的`asynchronous`行为，它可以基于一个特定的任务来等待我们。请参见下面的代码示例，以便更清楚地理解它:

我们将一个函数(printNames)作为参数传递给了另一个函数(addNewName)。这一次`printNames`只有在前面的功能执行完之后才会执行。这个概念被称为回调，你可以看到，在前一个函数完成任务后，我们调用了这个函数。这就是我们把回调称为稍后调用的原因。

在回调的基础上构建了许多高级概念。你可能也听说过`Callback hell`这个概念。我将在以后的文章中讨论这个问题。

# 结论

在本教程中，我们已经介绍了 JavaScript 的三个基本概念(Currying、Closure 和 Callback)。我希望这篇文章对你有所帮助和启发。

```
**Want to Connect?**This article was originally posted on [realjavascriptproject.com](https://realjavascriptproject.com/javascript-currying-closure-callback/)Connect with me on [LinkedIn](https://www.linkedin.com/in/rahulbanerjee2699/)
```