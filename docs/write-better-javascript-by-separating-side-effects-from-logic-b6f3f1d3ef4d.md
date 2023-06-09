# 通过将副作用与逻辑分离，写出更好的 JavaScript

> 原文：<https://betterprogramming.pub/write-better-javascript-by-separating-side-effects-from-logic-b6f3f1d3ef4d>

## 当逻辑是纯粹的，它更容易理解和测试

![](img/018e4bd1f3aaaced0f4e33b70f7796aa.png)

图片来源:作者

我们每天编写的软件系统中的代码会产生副作用。

它产生的一些副作用是永久性的，比如在数据库中创建记录和修改文件系统。其他的是暂时的，比如更新用户界面，打印到控制台，或者在内存中缓存信息。点亮智能灯或向太空发射火箭也是副作用。

一个没有副作用的软件系统可能没有价值。即使它计算出了生命的意义，也无法在不产生副作用的情况下进行交流。这样的系统将是一个黑匣子，没有人知道里面发生了什么。

但是副作用也使得代码更难推理。函数式编程(FP)旨在尽可能减少副作用。

我们如何在 JavaScript 代码中创建副作用？

本文将向您展示一个带有副作用的简单验证函数到一个更纯粹、更实用的版本的转换。我会尽可能推迟副作用的出现来达到这个目的。通过这个例子，我旨在向您展示在日常代码中使用的模式。

我们开始吧。

# 任务:验证用户名

这是一个验证用户名的简单而老派的函数。业务逻辑要求我们验证三件事:

*   用户名必须是非空字符串。
*   用户名必须包含字母数字字符。
*   用户名必须是唯一的。

简单的验证实现

这个版本验证了这三种情况，并在遇到问题时在用户界面上显示错误。它有什么问题？

有几件事:

*   这段代码不仅仅是验证，还将业务逻辑和副作用混合在一起。它知道如何以一种非常神奇的方式将错误可视化。从这个角度来看，它违反了*年代*年的坚实原则。尽管 SOLID 传统上适用于面向对象编程，但它的许多价值也适用于函数式编程。具有单一职责的功能通常更可取，也更容易组合。
*   因为它访问文档对象模型(DOM)，所以它的成功依赖于 id 为`error-text`的 DOM 元素的存在。
*   它依赖于特定的服务器 API 来工作。这种依赖性不能从逻辑的其余部分中提取出来。如果您将这段代码复制粘贴到其他项目中，您也需要复制 API。
*   它不是一个纯粹的功能，因为它会产生副作用。当您运行它时，它会发出一个网络请求并修改 DOM。
*   为这个函数编写单元测试既烦人又困难。您需要模拟 fetch 和 DOM 来验证用户名验证的业务逻辑是否写得正确。

# 第一次尝试

让我们开始从中分离副作用。我们首先可以分离的是对 DOM 的副作用。为此，我们重构了`validateUsername`函数，返回一个具有`valid`和`reason`属性的对象，并在`showError`函数中更新其外部的`error-text`。

这个版本稍微好一点，因为它把对`error-text`元素的副作用从`validateUsername`函数中分离出来。副作用被转移到`showError`函数中，该函数除了创建副作用之外什么也不做。单元测试现在也更加简单，因为不需要 DOM 来测试它。

但它仍然是一个不纯的函数，因为它执行网络调用。因为这个网络调用是一个可观察到的效果，我们也需要移除它。

# 第二次尝试

消除网络副作用的一种方法是将其作为参数传递给`validateUsername`函数。

当我们将`isUsernameAvailable`函数作为参数传递给`validateUsername`时，我们在技术上使`validateUsername`没有副作用。现在您可以对它进行单元测试，而不需要模拟网络。

但是它仍然有两个问题:

1.  唯一性检查与错误消息分开。任何人都可以通过一个任意的检查函数，该函数除了检查惟一性之外，还会得到相同的错误消息。
2.  `validateUsername`执行三种不同的检查。理想情况下，我们可以将它们分成独立的功能。这样，代码将更具可读性和可测试性。

# 第三次尝试

让我们试着将每个检查分开，并从它们中组成一个验证函数。

在这个版本中，`validateUsername`是复合的，而不是直接定义的。这个版本的优点是:

*   验证逻辑与实际的验证是分离的。`defineValidator`和`createRule`函数对用户名一无所知。您可以用任意的测试值分别测试这些函数。另一方面，`validNonEmptyString`、`validUsername`和`isUniqueUsername`函数不知道它们是如何组成的。这意味着您可以单独测试它们中的每一个。
*   您可以重用`defineValidator`和`createRule`函数来组成不同的验证函数。
*   每个功能都可以移动到不同的模块中。
*   与每个检查相关的错误消息与逻辑相分离。这允许我们重用一个带有不同错误消息的验证函数。例如，您可以使用`validNonEmptyString`函数来验证带有不同错误消息的电子邮件字段。
*   执行实际验证的函数很简单，更容易阅读和理解。
*   您可以通过添加新规则来轻松扩展验证。例如，如果您想防止用户使用众所周知的用户名，如`admin`或`moderator`，您可以定义一个新的验证函数并扩展`validateUsername`。

# 外卖食品

当您将业务逻辑与副作用混合在一起时，代码变得难以理解、推理、分割和单元测试。但是由于副作用是不可避免的，一种方法是在业务逻辑代码之外单独执行它们。

这样，副作用会在代码中的显式位置执行。当逻辑是纯粹的时候，更容易理解和测试。你也可以将纯粹的功能拆分成更小的功能，或者毫无顾虑地将它们组合起来。

编码快乐！