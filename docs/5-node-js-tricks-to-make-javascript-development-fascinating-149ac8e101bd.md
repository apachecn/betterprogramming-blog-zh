# 使 JavaScript 开发引人入胜的 5 个 Node.js 技巧

> 原文：<https://betterprogramming.pub/5-node-js-tricks-to-make-javascript-development-fascinating-149ac8e101bd>

## 让发展变得有趣，让你的生活更有成效

![](img/1c5f429bffecbf473970c1239463b7cf.png)

由 [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

[Node.js](https://nodejs.org/en/) 是开发应用程序最流行的平台之一，因为它具有极快的处理 I/O 操作的能力。但是它受欢迎还有很多其他的原因。

本文将介绍一些有用的技巧，您现在就可以在 Node.js *中使用这些技巧来增强您的开发体验，直到您着迷为止。由于 JavaScript 语言的灵活性，这些技巧中的大部分都是可行的。事不宜迟，我们开始吧！*

# 1.在控制台中检查代码(增强)

我想我们都经历过这种情况——在开发过程中，在我们的所有功能中调用`console.log`。调试代码可能会变得非常枯燥，因为我们需要的大多数信息在默认情况下并没有真正打印在控制台中。

例如，想象下面的这个`Page`类，它有一些关于上一页、当前页、导航到的页面的历史等的状态。导航后:

如果我们试图在导航后记录我们的`Page`实例，并想检查我们的实例是否行为正确，我们得到如下输出:

哇，这很有用！不是。幸运的是，Node.js 团队提供了一个方便的特性(以及您将在本文中找到的其他特性),我们可以立即在代码中使用它来改进我们的输出。

你所要做的就是将下面一行作为*方法*附加到一个对象上，并返回一个将被打印到控制台的对象:

例如，我们可以将它附加到我们的`Page`类，如下所示:

现在，当我们将`Page`登录到控制台时:

相反，我们可以将快照结果视为输出:

精彩！

# 2.纤维化

除了我们之前的技巧之外，还有一个巧妙的技巧，我们可以定制对象的字符串化。

例如，和以前一样，当被`toString()`调用时，我们的`Page`类的默认字符串化输出对我们来说不是很有用:

这很重要的原因是，当我们将对象指定为属性时，也会用到`toString()`的返回值，因为默认情况下会调用它:

输出:

幸运的是，我们可以覆盖默认的`toString()`方法，并提供一种更合适的方法来序列化成一个键:

如果我们想要缓存和恢复(或者更传统的术语，“T1”)我们的应用程序以前的状态，以便为用户提供更好的用户体验，这可能会很有用。例如，如果输出被缓存到本地存储，用户退出并在 7 个小时后返回，我们可以通过获取最后一次缓存的状态并相应地初始化它，使我们的应用程序自动恢复:

`Page`构造函数可以把它作为一个参数，并相应地初始化它的状态:

# 3.发音

与前面的技巧类似，当我们在`Page`类上调用`JSON.stringify`时，也可以定制输出。默认情况下，我们在使用时会得到以下信息:

输出:

这让我们回到了起点，我们一开始就直接登录了`Page`。

当我们覆盖默认的`toString()`方法以便它可以被转换成我们之前想要的字符串时，我们不得不调用`page.snapshot`来字符串化我们想要的输出:

我们实际上可以让快照成为在其上调用`JSON.stringify`的输出(这更好，因为它更符合在使用 API 时用于序列化数据的`JSON.stringify`和`JSON.parse`的本质)。

您所要做的就是像这样定义一个定制的`toJSON`方法:

现在，当我们将`Page`传递给`JSON.stringify`时，它会给我们快照:

# 4.以正确的方式清空终端

你可能对`console.clear`很熟悉，它看似清空了控制台。但是当你向上滚动时，前面的输出实际上还在。

清除所有数据并重新开始的正确方法是:

因为这对我来说是一个常见的需求，所以在 VSCode 上将它设置为用户代码片段，以便在我键入`clear`时自动生成它(它可以是另一个类似`cls`的关键字，但我选择了`clear`)是非常有用的。

要将它放入您的代码片段中，请按 Ctrl+P，选择“首选项:配置用户代码片段”，并选择`typescript.json`(或`javascript.json`，两者都选，或者甚至`typescriptreact.json`也为`.tsx`文件启用)并将它放入 JSON 对象中:

现在，当您在一个`.ts`文件中键入`clear`并按回车键时，它将生成:

# 5.前置，后置

在开发`package.json`中的应用程序时，我们都使用常用的`start`和`build`命令:

但是我们可以定义另一个脚本在主脚本执行完之前和/或执行完之后运行*。*

例如，要在用户对您的 repo 运行`npm install`之后运行一个文件，您所要做的就是在脚本名称前添加另一个带有前缀`post`的脚本命令。例如:

在[这个要点](https://github.com/gatsbyjs/gatsby/blob/master/packages/gatsby-cli/scripts/postinstall.js)中，Gatsby 在他们的 repo 上运行`npm install`后自动打印一条欢迎消息给用户。

这两个前缀可以用于任何脚本的*，并且它仍然会在主脚本之前运行带有`pre`前缀的匹配脚本，在主脚本之后运行带有`post`前缀的匹配脚本，因此您可以得到类似于:*

在文档中阅读更多关于 npm 脚本技巧[的信息。](https://docs.npmjs.com/cli/v7/using-npm/scripts#pre--post-scripts)

# 结论

本文到此结束。我希望你发现这是有价值的。以后多多关照！