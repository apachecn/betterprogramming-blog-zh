# 加速开发过程的 3 个奇妙的 JavaScript 技巧

> 原文：<https://betterprogramming.pub/3-marvelous-javascript-tips-to-speed-up-the-development-process-ba1bb8e3fb7a>

## 下面是我最喜欢的节省时间的 JavaScript 技巧

![](img/a5dcc5e1e430dc55902a48f8bed9ba90.png)

插图由[壁纸访问](https://wallpaperaccess.com/react-js)

最近，由于背后的社区，JavaScript 已经成为科技行业中“编写一次，到处运行”的语言。使用它，你几乎可以编写任何程序。可以是 web、 [mobile](https://reactnative.dev/) 或 [desktop](https://www.electronjs.org/) 应用。你甚至可以用它来创建[神经网络](https://brain.js.org/#/)或者编程[无人机](https://github.com/drone/drone-js)。是的，这种语言非常强大，但是在某些情况下拥有一些精心定制的技巧才是一个优秀的 JavaScript 开发人员，拥有这些技巧需要很多很多年的经验。

因此，这里有 3 个我最喜欢的节省时间且非常有用的 JavaScript 技巧。

# 1.创建异步上下文

在 JavaScript 中处理异步事件有点令人困惑，尤其是对初学者来说，因为它与[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)直接相关，简单来说，代码执行由开发人员控制。在 ES6 (2015)之前，异步代码需要进行如下处理:

在 ES6 之前，应该有很多 then 和 catch 方法是连锁的。

然而，在 ES6 中，引入了一个新的非常有用的关键字:`await`。使用它，您可以简单地等待任何异步任务完成并继续执行。一切都很完美，除了一个大警告。`await`必须在`async`函数中使用，不能在全局范围内使用。因此，我们将使用[life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)来模拟一个全局级别的异步上下文，我们可以在任何需要的地方使用`await`。

使用 IIFE，您可以轻松地创建异步上下文来处理各种异步任务。

# 2.命名函数参数

通常，当你定义一个函数时，你会有可以在调用时传递的参数。您用逗号(，)将它们分开，然后一个一个地传递它们。如果您有几个参数和一个很好的 IDE 或带有 IntelliSense 的文本编辑器，这完全没问题。但是如果没有，那么你必须按顺序记住所有的参数，这有点累人。

有许多参数的函数很难记忆和书写。

然而，在 ES6 的一点帮助下，我们可以创建具有命名参数的函数，这更容易读写。我们可以传递包含所有参数的单个参数对象，而不是一个接一个地传递参数，这样我们就不必记住顺序。此外，在函数中，我们可以析构对象并单独获取参数。

有了析构参数，调用函数变得更加明确和容易。

# 3.Try/Catch 块的功能包装器

随着`async/await`越来越流行，`[catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)`的使用量有所下降；这就是为什么开发人员开始使用`try/catch`块来处理异步函数中的错误。然而，另一个块意味着另一个层次的缩进和另一个层次的混乱。

使用 try/catch 处理异步函数中的错误，完全重置了“await”相对于“then”的缩进优势。

但是，如果我们有一个基于成功而不缩进地返回错误或结果的函数，那不是很好吗？这正是我们将要实施的。我们用一个函数包装`try/catch`块；然后，我们用它来捕捉没有任何缩进的错误。

错误处理函数将是一个异步函数，因为我们想在里面使用`await`,并接受另一个异步函数作为参数。然后，基于参数函数的成功，将返回结果或错误。

将 try/catch 封装在函数内部让我们可以在不缩进的情况下使用它。

使用这个`handled`函数，您可以像`await`一样处理相同缩进级别的错误。你所要做的就是用`handled`包装异步函数，并析构响应和错误。

“已处理”函数返回错误和响应，但基于成功，其中一个为“空”。

[![](img/9617ff4f7d33119c4d4e3d20c90708a5.png)](https://mailchi.mp/a45f8fb96cc9/subscribe)

订阅获得你的**免费*订户专属*** 故事。

干得好！现在你知道了我每天用来放松生活的三个神奇的 JavaScript 技巧，我想你也会用到它们。感谢阅读。

## 编辑

你可以从这里读到这个故事的续集。

[](https://medium.com/fractions/3-more-marvelous-javascript-tips-to-skyrocket-your-productivity-ba75f72b0195) [## 3 个更神奇的 JavaScript 技巧，让你的生产力一飞冲天

### 在 JS 中有很多方法可以实现同样的事情。然而，你的成就显示了你的经验。

medium.com](https://medium.com/fractions/3-more-marvelous-javascript-tips-to-skyrocket-your-productivity-ba75f72b0195)