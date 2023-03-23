# 将 CSV 转换为 JavaScript 对象数组

> 原文：<https://betterprogramming.pub/convert-a-csv-to-a-javascript-array-of-objects-3a0914a0bd8d>

## 这比你想象的要简单

![](img/f4a75bc2e9a3a59d7ecffea69a9ff3d1.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

将 CSV 文件转换为 JavaScript 对象数组的最简单方法是使用 JavaScript split、map、forEach、trim 方法和 spread 运算符。我很乐意在本文中向您展示如何使用文件上传表单。

> 在本文中，我希望您了解一些 JavaScript 知识，因为这将使您更容易理解。如果没有，我会尽我所能给你尽可能多的信息。

## TL；速度三角形定位法(dead reckoning)

如果您只需要将 CSV 值转换为 JavaScript 对象数组的代码，请复制并粘贴代码。

但是如果你想理解代码中的一切？我建议您继续阅读或者向下滚动到第 3 步，在那里我会解释它的逻辑。

# 1.创建用于上传 CSV 文件的表单

首先，我们需要一个 HTML 表单来上传文件。

由于我没有关注用户友好的表单，而是更多地关注将 CSV 转换为对象数组，所以我添加了最少的 CSS。

对于结果，你可以检查 [CodeSandbox](https://codesandbox.io/s/how-to-convert-csv-to-javascript-array-of-objects-e6e3nc) 。

# 2.从 CSV 文件中读取内容

现在我们需要访问 JavaScript 中的三样东西。

1.  表单:用于访问文件阅读器
2.  输入:用于选择 CSV 文件
3.  Textarea:用于显示对象数组

当用户选择一个 CSV 文件并点击“提交”按钮时，我们想要读取它的内容，这样我们就可以将它转换成一个对象数组。

我们从表单上的一个`addEventlistener`开始，我们监听一个提交事件。

因为提交事件的自然行为是页面刷新，所以我们想取消这个行为。否则，我们将无法读取文件及其内容。我们用`event.preventDefault()`来做这件事。

现在我们想访问文件的内容。我们可以通过使用`FileReader` API 来实现(阅读关于 FileReader AP I 的更多 MDN Web 文档)。这个 API 帮助我们访问用户选择的文件。

在上面的代码中，我们为第一个选择的文件创建了`file`变量。然后我们通过调用`new FileReader()`在`reader`变量中创建一个新的 FileReader 实例。

其次，我们跳到代码片段的底部。`reader.readAsText(file)`将在读取过程完成后将 blob 转换为文本。

当这个过程完成后，我们有了`reader.onload`事件，在这里我们可以访问文件的内容。这里我们使用`e`来引用事件，因为我们在`addEventListener("submit", {...})`中使用了`event`，这样我们就防止了冲突的发生。

控制台记录内容以测试是否一切正常。

# 3.将 CSV 转换为对象数组

现在我们开始研究将 CSV 内容转换成一组对象的基本方法。

我们知道 CSV 可以有多种形式和大小。我发现在前端处理大型 CSV 文件会很麻烦，所以不要在生产应用程序中这样做。

较小的 CSV 文件没有问题。不过自己测试这个还是不错的。

我们如何将这个 CSV 内容转换成 JavaScript 对象数组？直截了当，循序渐进！

## 3.1 CSV 内容

让我们将这个 CSV 内容添加到一个变量中，这样我们就可以用它来测试我们的功能。

*我用来测试它的文件结合* [*文件上传可以在我的 gist 上找到。*](https://gist.github.com/devbyray/2fcd917bbe92ee660853a0eff9833bff)

## 3.2 创建功能

我们想为内容添加一个参数，所以我们称之为`stringValue`。

## 3.3 清理您的内容

首先，我们需要用修剪空格来清理 CSV 的内容。我们用`trim()`方法来做。

## 3.4 新行拆分

我们希望 CSV 文件中的每一行都成为数组中的一个对象。所以我们需要把所有的内容分开。我们通过在`trim()`方法之后链接`split('\n')`方法来实现这一点。

这个函数将返回一个字符串数组。尝试放入上面的 CSV 内容并调用函数时，检查控制台的结果。

## 3.5 将属性名称与值分开

在示例中，我们看到 CSV 的属性名在第一行。下面几行是这些属性中的值。让我们使用 destructuring 和 spread 语法将这些键从值中分离出来(阅读 MDN Web 文档上关于 [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 和 [spread 语法的更多信息)。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

多亏了析构，我们现在有了一个带有`keys`的变量和一个带有所有其余值的变量`rest`。

## 3.6 拆分属性值

接下来，我们需要将长串值拆分成不同的值，这样我们就可以在下一步中使用它们来创建对象。我们通过链接`map()`方法并使用`split(',')`方法创建一个值数组来实现。

## 3.7 创建一个对象数组

现在我们已经准备好了数据，我们可以创建一个对象数组。我们对`rest`变量使用了`map()`方法，这样我们就可以遍历多维数组。

对于每个值的数组，我们循环遍历这些键。所以每个键都有它的值，即使值是一个空字符串，也不会有问题，因为我们以键为基础。

在`map`中的每一次迭代都将创建一个对象并将其返回给数组。最终，函数将返回对象数组。

在我的 RunKit 示例中，您可以看到代码是有效的。

## 3.8 在表单中实现功能

现在我们准备将函数实现到表单中。下面的代码是 JavaScript 的结果。

当我们把 HTML 和 JavaScript 结合起来，你会得到这样一个结果。在我的例子中，我在`<textarea>`元素中展示了 JSON。用`JSON.stringify(csvArray, null, 4)`我可以很好地格式化它。

好了，就这样吧！现在我们有了一个 HTML 表单，它用 JavaScript 将 CSV 文件转换成一个对象数组💪。干得好！

请分享如果你创造了一个不同的版本或它看起来更友好的用户。

# 谢谢！

![](img/34b6d4e88daa5f22c87d2b9b476f66e5.png)

读完这个故事后，我希望你学到了一些新的东西，或者受到启发去创造一些新的东西！🤗

如果我给你留下了问题或一些要说的话作为回应，向下滚动并给我键入一条消息。如果你想保密，请在 Twitter @DevByRayRay 上给我发一条 [DM。我的 DM 永远是开放的😁](https://twitter.com/@devbyrayray)

[**通过电子邮件获取我的文章点击这里**](https://byrayray.medium.com/subscribe) **|** [**购买 5 美元中等会员**](https://byrayray.medium.com/membership)

## 阅读更多

![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----3a0914a0bd8d--------------------------------)

## 最新的 JavaScript 和 TypeScript 故事

[View list](https://byrayray.medium.com/list/latest-javascript-typescript-stories-0358ad941491?source=post_page-----3a0914a0bd8d--------------------------------)14 stories![](img/c93ca03b33796c40dcc47873de2697c2.png)![](img/86f37efa11855f6f0f0f62984c37f696.png)![](img/ddbaa6d0bea676316247e82043d60b63.png)