# 闭包、奉承和酷的抽象

> 原文：<https://betterprogramming.pub/closures-currying-and-cool-abstractions-89221e565eb0>

## JavaScript 中的函数式编程

![](img/d3caa500e07ced68b936b4a27fbb3350.png)

[丘特尔斯纳](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

这篇文章最初发表在 [*TK 的博客*](https://leandrotk.github.io/tk/2020/03/closure-currying-and-cool-abstractions/index.html) 。

在本文中，我们将讨论闭包、curried 函数，并利用这些概念来构建很酷的抽象。我想展示每个概念背后的想法，但也想通过例子和重构代码使它变得非常实用，从而使它更有趣。

# 关闭

闭包是 JavaScript 中的一个常见话题，我们将从它们开始。正如 MDN web 文档所定义的:

> 一个闭包是一个函数的组合，这个函数被捆绑(封装)在一起，并引用它周围的状态(词法环境)

基本上，每次创建一个函数，都会创建一个闭包。它提供对所有状态(变量、常数、函数等)的访问。)并被称为`lexical environment`。

让我们看一个简单的例子:

这是什么？

*   我们的主要功能叫做`makeFunction`。
*   一个名为`name`的常量被赋予一个字符串`'TK'`。
*   `displayName`函数的定义(它只记录了`name`常数)。
*   最后`makeFunction`返回`displayName`函数。

这只是一个函数的定义。当我们调用`makeFunction`时，它将在其中创建所有东西:在本例中是常量和函数。

正如我们所知，当`displayName`函数被创建时，闭包也被创建，它使函数意识到环境——在本例中是`name`常量。这就是为什么我们可以不破坏任何东西。该函数知道词法环境。

太好了！果然有效！`makeFunction`的返回是我们存储在`myFunction`常量中的一个函数，稍后调用，它显示`TK`。

我们也可以让它像箭头一样工作:

但是如果我们想传递名称并显示它呢？一个参数！

现在我们可以玩这个名字了:

创建的函数不仅知道常量/变量，还知道其他函数。

这也是可行的:

返回的函数知道`display`函数，并且能够调用它。

一种强大的技术是使用闭包来构建“私有”函数和变量。

几个月前，我正在学习数据结构(再次！)并想实现每一个。但是我总是使用面向对象的方法。作为一个函数式编程爱好者，我希望按照 FP 原则(纯函数、不变性、引用透明性等)构建所有的数据结构。).

我学习的第一个数据结构是堆栈。这很简单。主要的 API 是:

*   `push`:将一个项目添加到堆栈的第一个位置。
*   `pop`:从堆栈中移除第一个项目。
*   `peek`:从堆栈中获取第一个项目。
*   `isEmpty`:验证堆栈是否为空。
*   `size`:获取堆叠的物品数量。

我们可以清楚地为每个“方法”创建一个简单的函数，并将堆栈数据传递给它。它使用/转换数据并返回数据。

但是我们也可以创建只公开 API 方法的私有堆栈数据。我们开始吧！

因为我们在`buildStack`函数中创建了`items`堆栈数据，所以它是“私有的”它只能在函数中访问。在这种情况下，只有`push`、`pop`等。可能接触到数据。这就是我们要找的。

我们如何使用它？像这样:

所以当栈被创建时，所有的函数都知道`items`数据。但是在函数之外，我们无法访问这些数据。这是隐私。我们只是通过使用堆栈内置 API 来修改数据。

# Currying

[Currying 是将一个有多个参数的函数转换成一系列只有一个参数的函数的过程](https://en.wikipedia.org/wiki/Currying)。

假设你有一个有多个参数的函数:`f(a, b, c)`。使用 currying，我们实现了返回函数`h(c)`的函数`f(a)`。

基本上，`f(a, b, c)`——>——T16。

让我们建立一个简单的例子:将两个数相加。但首先，不要阿谀奉承！

太好了！超级简单！这里我们有一个带有两个参数的函数。要将它转换成一个 curried 函数，我们需要一个接收`x`并返回一个接收`y`并返回两个值之和的函数。

我们可以将这个`addY`重构为一个匿名箭头函数:

或者通过构建一行箭头函数来简化它:

这三个不同的 curried 函数具有相同的行为:它们构建了一个只有一个参数的函数序列。

我们如何使用它？

起初，它可能看起来有点奇怪，但它背后有一个逻辑。`add(10)`返回函数。我们用`20`值调用这个函数。

这与以下内容相同:

这很有趣。我们可以通过调用第一个函数来生成专门的函数。假设我们想要一个`increment`函数。我们可以通过将`1`作为值传递来从我们的`add`函数中生成它。

当我实现 [Lazy Cypress](https://github.com/leandrotk/lazy-cypress) 时，一个 npm 库记录表单页面中的用户行为并生成 Cypress 测试代码，我想构建一个函数来生成字符串`input[data-testid="123"]`。所以这里我们有元素(`input`)、属性(`data-testid`)和值(`123`)。在 JavaScript 中插入这个字符串将会是这样的:`${element}[${attribute}="${value}"]`。

想到的第一个实现是接收这三个值作为参数，并返回上面的插值字符串。

这很棒。我实现了我所期待的。但同时，我想构建一个更习惯的函数。我可以写类似于"*得到一个属性为 Y，值为 z 的元素 X .*"那么如果我们把这个短语分成三个步骤呢？

*   *“获取一个元素 X*”:`get(x)`
*   *“带属性 Y*”:`withAttribute(y)`
*   *和 Z 值*:`andValue(z)`

我们可以利用 currying 概念将`buildSelector(x, y, z)`转化为`get(x)``withAttribute(y)``andValue(z)`。

这里我们使用一个不同的想法:用一个函数作为键值返回一个对象。这样就可以实现这个语法:`get(x).withAttribute(y).andValue(z)`。

对于每个返回的对象，我们有下一个函数和参数。

重构时间！删除`return`语句:

我觉得它看起来更漂亮。我们是这样使用它的:

`andValue`函数知道`element`和`attribute`的值，因为它知道词法环境，就像我们之前讨论闭包一样。

我们也可以使用“部分 currying”来实现函数仅将第一个参数与其余参数分开作为示例。

做了很长时间的 web 开发，我通常使用[事件监听器 web API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) 。它是这样使用的:

我想创建一个抽象来构建专门的事件侦听器，并通过传递元素和回调处理程序来使用它们。

这样，我可以创建不同的专用事件侦听器，并将它们用作函数。

有了这些概念，我可以使用 JavaScript 语法创建一个 SQL 查询。我想用 SQL 查询 JSON 数据，如下所示:

所以我构建了一个简单的引擎来处理这个实现:

有了这个实现，我们可以用 JSON 数据启动引擎:

并将其用作 SQL 查询:

# 结论

今天到此为止。我们可以继续展示很多不同的抽象的例子，但是我会让你玩这些概念。

# 资源

*   [文章的源代码](https://github.com/tk-notes/blog/tree/master/closures-currying-and-cool-abstractions)
*   [闭包| MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
*   [Currying | Fun Fun 函数](https://www.youtube.com/watch?v=iZLP4qOwY8I)