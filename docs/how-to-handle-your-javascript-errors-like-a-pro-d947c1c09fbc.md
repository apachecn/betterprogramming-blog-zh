# 如何像专家一样处理 JavaScript 错误

> 原文：<https://betterprogramming.pub/how-to-handle-your-javascript-errors-like-a-pro-d947c1c09fbc>

## 理解 JavaScript 中错误处理的快速指南

![](img/cb1da06d61e9c2bc0340bd66aea5201e.png)

照片由[拉迪克·西蒂科夫](https://unsplash.com/@radya?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

错误处理是除 if-else 和循环之外的另一种形式的控制结构。

在 JavaScript 中，总是很难避免所有的错误。除了 bug、逻辑错误或语法错误之外，作为开发人员，您还可能面临一些无法控制的错误。

例如，用户输入错误。用户输入的电子邮件没有“@”字符，这应该是无效的。错误的另一个例子可以是网络错误。您可以与服务器通信，但该服务器可能处于脱机状态。

在这两个例子中，你无法控制局面。但是，您可以限制用户选项。你可以说没有放“@”字符，邮箱是无效的。对于服务器示例，您需要抛出错误并捕获错误，以便优雅地失败。您还可以向用户显示解释性错误消息或一些回退代码来恢复您的代码。

假设您接受来自用户的输入，如下所示:

```
const userInput = prompt(“Enter your lucky number”);
let luckyNumber = parseInt(luckyNumber);
```

这里，`parseInt()`可能有问题。用户可以输入一些值，而不是输入类似于`"qwerty123"`的值。在这种情况下，`parseInt()`将在您的 JavaScript 代码中返回`NaN`。您可能希望使用 if-else 语句来处理此错误。

```
if(isNan(luckyNumber) || luckyNumber <=0){
  luckyNumber = Math.floor(Math.random() * 101);
}
```

但是，为了处理您的代码，您也可以引发如下所示的错误:

要旨由[作者](https://medium.com/@onurinanc)

# 你怎么能抛出你的自定义错误？

当您的项目越来越大时，您需要温和地处理错误。如果你抛出类似`"There is an Error"`的错误信息，你将很难理解项目中错误的真正含义。

因此，如果您针对不同的情况定制您的错误，那将是最好的。这样做将有助于您轻松处理错误。例如，如果您从事网络操作，您可能需要`HttpError`，数据库操作`DatabaseError`，以及搜索操作`NotFoundError`。

要创建您的特殊错误类，您可以从 JavaScript 内置错误类扩展它们。下面是 JavaScript 定义的`Error`类:

[作者的要旨](https://medium.com/@onurinanc)

您可以扩展它，如下所示:

作者简介

此外，由于`ValidationError`是通用的，您可以将其扩展到`PropertyRequiredError`:

[作者的要旨](https://medium.com/@onurinanc)

# 使用 Try-Catch 处理错误

使用 try-catch 控制结构，您可以尝试可能失败的代码，并捕获代码中引发的潜在错误。

将所有代码文件放在一个`try`块中是一个糟糕的想法。如果是这样，你的代码看起来会很糟糕，代码的性能也会降低。相反，您可以调试您的逻辑错误。如果您只想将代码移动到一个`try`块中，而您无法控制它是否会抛出错误，这将是最好的，就像上面的例子一样。

例如，您可以将`getLuckyNumber`放入 try-catch 块中。这种控制结构的思想是，如果 try 块被执行，那么 catch 块将不会被执行。所以，如果没有任何错误，catch 块将不起作用。此外，如果 try 块不起作用，将执行 catch 块。

要旨由[作者](https://medium.com/@onurinanc)

因此，如果用户输入一个不完全是数字的值，上面的`catch`块将被执行，并且`luckyNumber`将等于 0 到 100 之间的一个随机数。然后，剩下的代码就可以工作了。

然而，这一招并不总是奏效。有时，您最多只能显示一条错误消息。例如，如果一个服务器离线，而你想在一个服务器上保存数据，这是行不通的。

正如你在上面读到的，你可以抛出你的自定义错误。现在，您可以在一个 try-catch 块中测试它。假设您有一个读取 JSON 数据并创建`car`的函数。

要旨由[作者](https://medium.com/@onurinanc)

在 try-catch 块中处理此错误，如下所示:

要旨由[作者](https://medium.com/@onurinanc)

# **终于**

另一个错误处理模块是`finally`。在`try-catch`之后可以用`finally`。也可以在`try` 块后使用。不管你有没有错误，里面的代码最终都会被执行。

在大多数情况下，你不需要`finally`，因为你有一个好的后备，就像上面的例子。有一些用例。例如，您可以使用 finally 块来清理代码，如关闭连接、关闭文件或释放线程。

但是，请记住，总是执行`finally` 块。

# 结论

错误处理是专业开发人员的必备技能。当项目变大时，处理错误变得更加重要。如果您只抛出消息“错误”，您可能不知道会遇到什么样的错误

此外，在某些情况下，您可能会使用 if-else 块来掩盖错误。但是，在某些情况下，您需要捕捉错误，比如使用网络连接。

以下是处理 JavaScript 错误的一些建议:

*   抛出错误消息以了解错误的类型
*   有效使用`try-catch`
*   您可以使用`finally`来清理代码，因为`finally` 块总是被执行
*   创建您的自定义错误类
*   您可以从内置的 JavaScript `Error`类扩展定制的错误类